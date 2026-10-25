# Backend & Database Architecture (BACKEND_STRUCTURE.md) — ATELIER

**Document Status:** LOCKED / SOURCE OF TRUTH  
**Database:** Supabase PostgreSQL with `pgvector` & Row-Level Security (RLS)

---

## 1. Relational Schema & Table Definitions

```sql
-- Enable Extensions
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "vector";

-- 1. Profiles (Users & Creators)
CREATE TABLE profiles (
  id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
  handle TEXT UNIQUE NOT NULL,
  display_name TEXT NOT NULL,
  avatar_url TEXT,
  bio TEXT,
  is_creator BOOLEAN DEFAULT false,
  is_boutique BOOLEAN DEFAULT false,
  body_measurements JSONB DEFAULT '{}'::jsonb, -- height, chest, waist, hips
  pose_landmarks JSONB DEFAULT '[]'::jsonb,    -- MediaPipe 33 landmark vector cache
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- 2. Wardrobe Items (Personal Closet)
CREATE TABLE wardrobe_items (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  category TEXT NOT NULL CHECK (category IN ('top', 'bottom', 'outerwear', 'footwear', 'dress', 'accessory')),
  raw_image_url TEXT NOT NULL,
  clean_image_url TEXT NOT NULL, -- Transparent cutout from RMBG-1.4
  color_palette TEXT[] DEFAULT '{}', -- Array of HEX codes
  brand TEXT,
  season TEXT,
  cost_per_wear NUMERIC(10,2) DEFAULT 0.00,
  wear_count INT DEFAULT 0,
  is_public BOOLEAN DEFAULT false,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 3. Outfits (Composed Looks)
CREATE TABLE outfits (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  title TEXT,
  occasion TEXT,
  rendered_image_url TEXT, -- Rendered avatar composite or VTON result
  is_favorite BOOLEAN DEFAULT false,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 4. Outfit Items (Junction Table)
CREATE TABLE outfit_items (
  outfit_id UUID NOT NULL REFERENCES outfits(id) ON DELETE CASCADE,
  wardrobe_item_id UUID NOT NULL REFERENCES wardrobe_items(id) ON DELETE CASCADE,
  PRIMARY KEY (outfit_id, wardrobe_item_id)
);

-- 5. Feed Posts (Social Discovery)
CREATE TABLE feed_posts (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  creator_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  caption TEXT,
  image_url TEXT NOT NULL,
  aspect_ratio NUMERIC(3,2) DEFAULT 0.80, -- 4:5 default
  likes_count INT DEFAULT 0,
  saves_count INT DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 6. Tagged Products (Affiliate & Boutique Garments on Posts)
CREATE TABLE post_tags (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  post_id UUID NOT NULL REFERENCES feed_posts(id) ON DELETE CASCADE,
  item_name TEXT NOT NULL,
  brand TEXT NOT NULL,
  price NUMERIC(10,2) NOT NULL,
  currency TEXT DEFAULT 'USD',
  clean_image_url TEXT NOT NULL, -- For instant VTON onto user avatar
  external_url TEXT NOT NULL,    -- Merchant destination or affiliate link
  affiliate_network TEXT,        -- 'skimlinks', 'sovrn', 'amazon', 'direct'
  pos_x NUMERIC(5,2) NOT NULL,   -- X percentage on image (0-100)
  pos_y NUMERIC(5,2) NOT NULL,   -- Y percentage on image (0-100)
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 7. Boutiques (Local Storefronts)
CREATE TABLE boutiques (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  owner_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  name TEXT NOT NULL,
  slug TEXT UNIQUE NOT NULL,
  description TEXT,
  city TEXT NOT NULL,
  address TEXT,
  banner_url TEXT,
  commission_fee_rate NUMERIC(4,2) DEFAULT 0.08, -- 8% platform fee
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 8. Boutique Products
CREATE TABLE boutique_products (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  boutique_id UUID NOT NULL REFERENCES boutiques(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  description TEXT,
  price NUMERIC(10,2) NOT NULL,
  category TEXT NOT NULL,
  clean_image_url TEXT NOT NULL,
  stock_quantity INT DEFAULT 1,
  buy_url TEXT NOT NULL, -- Direct checkout or Stripe payment link
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 9. Affiliate & Conversion Clicks
CREATE TABLE affiliate_clicks (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  tag_id UUID NOT NULL REFERENCES post_tags(id) ON DELETE CASCADE,
  user_id UUID REFERENCES profiles(id) ON DELETE SET NULL,
  creator_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  target_url TEXT NOT NULL,
  ip_hash TEXT,
  user_agent TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 10. Post Comments
CREATE TABLE post_comments (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  post_id UUID NOT NULL REFERENCES feed_posts(id) ON DELETE CASCADE,
  user_id UUID NOT NULL REFERENCES profiles(id) ON DELETE CASCADE,
  content TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

---

## 2. Row Level Security (RLS) Policies

```sql
-- Enable RLS on all tables
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE wardrobe_items ENABLE ROW LEVEL SECURITY;
ALTER TABLE outfits ENABLE ROW LEVEL SECURITY;
ALTER TABLE outfit_items ENABLE ROW LEVEL SECURITY;
ALTER TABLE feed_posts ENABLE ROW LEVEL SECURITY;
ALTER TABLE post_tags ENABLE ROW LEVEL SECURITY;
ALTER TABLE boutiques ENABLE ROW LEVEL SECURITY;
ALTER TABLE boutique_products ENABLE ROW LEVEL SECURITY;
ALTER TABLE affiliate_clicks ENABLE ROW LEVEL SECURITY;
ALTER TABLE post_comments ENABLE ROW LEVEL SECURITY;

-- Profiles: Public can view, owner can update
CREATE POLICY "Public profiles are viewable by everyone" ON profiles FOR SELECT USING (true);
CREATE POLICY "Users can update own profile" ON profiles FOR UPDATE USING (auth.uid() = id);

-- Wardrobe Items: Only owner can view and mutate personal wardrobe
CREATE POLICY "Users can view own wardrobe" ON wardrobe_items FOR SELECT USING (auth.uid() = user_id OR is_public = true);
CREATE POLICY "Users can insert own wardrobe" ON wardrobe_items FOR INSERT WITH CHECK (auth.uid() = user_id);
CREATE POLICY "Users can update own wardrobe" ON wardrobe_items FOR UPDATE USING (auth.uid() = user_id);
CREATE POLICY "Users can delete own wardrobe" ON wardrobe_items FOR DELETE USING (auth.uid() = user_id);

-- Feed Posts & Tags: Publicly viewable
CREATE POLICY "Feed posts viewable by everyone" ON feed_posts FOR SELECT USING (true);
CREATE POLICY "Creators can insert feed posts" ON feed_posts FOR INSERT WITH CHECK (auth.uid() = creator_id);
CREATE POLICY "Tags viewable by everyone" ON post_tags FOR SELECT USING (true);

-- Boutiques: Publicly viewable
CREATE POLICY "Boutiques viewable by everyone" ON boutiques FOR SELECT USING (true);
CREATE POLICY "Boutique products viewable by everyone" ON boutique_products FOR SELECT USING (true);
```

---

## 3. Supabase Storage Buckets

| Bucket Name | Access Level | Allowed MIME Types | Max Size | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| `wardrobe-raw` | Authenticated | `image/jpeg, image/png, image/webp, image/heic` | 15MB | Original uncropped user camera snaps. |
| `wardrobe-clean` | Authenticated | `image/png, image/webp` | 5MB | Segmented transparent cutouts for VTON. |
| `avatars` | Public | `image/jpeg, image/png, image/webp` | 10MB | User avatar photos and standardized mannequin bases. |
| `feed-posts` | Public | `image/jpeg, image/png, image/webp` | 20MB | High-resolution editorial lookbook imagery. |
