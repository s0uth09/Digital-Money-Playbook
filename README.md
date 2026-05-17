# THE DIGITAL MONEY PLAYBOOK

Premium digital education platform with crypto payments and instant product delivery.

## 🎨 Design Philosophy: "The Sovereign Architect"

**High-Utility Minimalism** - Elite software tool aesthetic with atmospheric depth and editorial tech minimalism.

### Visual Identity
- **Atmospheric Depth**: Layered blacks and charcoals creating cinematic space
- **Electric Accent**: Vivid purple (#8B5CF6) transitioning to deep cobalt blue (#3B82F6)
- **Glass Morphism**: Floating glass panels with 10% opacity, 1px borders, 20px backdrop blur
- **Inner Glows**: Apple-style lighting from top-left (not drop shadows)

### Typography
- **Headlines**: Inter Tight (medium weight, -0.02em letter spacing)
- **Body**: Inter (regular, 1.6 line height)
- **System Labels**: JetBrains Mono (uppercase, 0.05em spacing)

---

## 🚀 Quick Start

### Prerequisites
```bash
Node.js 18+ 
npm or yarn
Supabase account
NOWPayments account
Resend account (for emails)
```

### Installation

1. **Clone and install dependencies**
```bash
cd digital-money-playbook
npm install
```

2. **Set up environment variables**
```bash
cp .env.example .env
```

Edit `.env` with your credentials:
```env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key

# NOWPayments
NOWPAYMENTS_API_KEY=your-api-key
NOWPAYMENTS_IPN_SECRET=your-ipn-secret

# App Config
NEXT_PUBLIC_URL=http://localhost:3000
NEXT_PUBLIC_ADMIN_EMAIL=admin@yoursite.com

# Email (Resend)
RESEND_API_KEY=your-resend-key
```

3. **Set up Supabase database**

Run this SQL in your Supabase SQL Editor:

```sql
-- Enable UUID extension
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Profiles (extends Supabase Auth)
CREATE TABLE profiles (
  id UUID REFERENCES auth.users PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  full_name TEXT,
  avatar_url TEXT,
  created_at TIMESTAMP DEFAULT NOW(),
  affiliate_code TEXT UNIQUE,
  total_spent DECIMAL DEFAULT 0
);

-- Products
CREATE TABLE products (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  title TEXT NOT NULL,
  slug TEXT UNIQUE NOT NULL,
  description TEXT,
  price DECIMAL NOT NULL,
  currency TEXT DEFAULT 'USD',
  cover_image_url TEXT,
  file_url TEXT,
  is_active BOOLEAN DEFAULT TRUE,
  category TEXT,
  created_at TIMESTAMP DEFAULT NOW(),
  sales_count INTEGER DEFAULT 0
);

-- Orders
CREATE TABLE orders (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES profiles(id),
  product_id UUID REFERENCES products(id),
  payment_id TEXT,
  payment_status TEXT CHECK (payment_status IN ('pending', 'confirming', 'confirmed', 'failed')),
  crypto_currency TEXT,
  crypto_amount DECIMAL,
  usd_amount DECIMAL,
  email TEXT,
  affiliate_code TEXT,
  created_at TIMESTAMP DEFAULT NOW(),
  completed_at TIMESTAMP
);

-- Coupons
CREATE TABLE coupons (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  code TEXT UNIQUE NOT NULL,
  discount_percent INTEGER,
  discount_fixed DECIMAL,
  max_uses INTEGER,
  current_uses INTEGER DEFAULT 0,
  expires_at TIMESTAMP,
  is_active BOOLEAN DEFAULT TRUE
);

-- Affiliate tracking
CREATE TABLE affiliate_clicks (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  affiliate_code TEXT NOT NULL,
  clicked_at TIMESTAMP DEFAULT NOW(),
  ip_address TEXT,
  converted BOOLEAN DEFAULT FALSE
);

-- Row Level Security
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
ALTER TABLE products ENABLE ROW LEVEL SECURITY;

-- Policies
CREATE POLICY "Public products are viewable by everyone"
  ON products FOR SELECT
  USING (is_active = true);

CREATE POLICY "Users can view own orders"
  ON orders FOR SELECT
  USING (auth.uid() = user_id);

CREATE POLICY "Users can view own profile"
  ON profiles FOR SELECT
  USING (auth.uid() = id);

-- Function to increment sales count
CREATE OR REPLACE FUNCTION increment_sales(product_id UUID)
RETURNS void AS $$
BEGIN
  UPDATE products
  SET sales_count = sales_count + 1
  WHERE id = product_id;
END;
$$ LANGUAGE plpgsql;
```

4. **Run development server**
```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000)

---

## 📁 Project Structure

```
digital-money-playbook/
├── app/
│   ├── page.tsx                    # Landing page (Sovereign Architect aesthetic)
│   ├── checkout/[productId]/       # Crypto checkout flow
│   ├── admin/                      # Admin dashboard
│   ├── success/                    # Payment success page
│   ├── api/
│   │   ├── payments/create/        # NOWPayments invoice creation
│   │   └── webhooks/nowpayments/   # Payment confirmation webhook
│   ├── layout.tsx
│   └── globals.css
├── components/                     # Reusable UI components
├── lib/
│   └── supabase.ts                # Supabase client + types
├── public/                         # Static assets
├── tailwind.config.js             # Design system config
└── package.json
```

---

## 🎨 Components Built

### 1. ✅ Landing Page
**Sovereign Architect Aesthetic**
- Atmospheric depth with layered backgrounds
- Electric purple/cobalt gradient accents
- Glass morphism UI cards with inner glows
- Smooth Apple-curve animations (cubic-bezier 0.16, 1, 0.3, 1)
- Bento grid "Seven Pillars" section
- Product cards with "slab" presentation
- Monospace system labels
- Legal disclaimer beautifully typeset

**Features:**
- Hero with animated stats
- Product showcase
- Feature highlights
- Process walkthrough
- Educational disclaimer

### 2. ✅ Checkout Flow
**Crypto Payment Integration**
- NOWPayments integration (BTC, ETH, SOL, USDT)
- Product selection with visual presentation
- Email collection for delivery
- Crypto currency selector
- Real-time payment status
- Order summary
- Security badges

**API Routes:**
- `/api/payments/create` - Creates NOWPayments invoice
- `/api/webhooks/nowpayments` - Handles payment confirmation

### 3. ✅ Admin Dashboard
**Analytics & Management**
- Three-tab interface (Overview, Products, Orders)
- Real-time stats cards:
  - Total Revenue
  - Total Orders
  - Active Products
  - Conversion Rate
- Revenue chart (6-month view)
- Product management table
- Order tracking system
- Search and filter functionality
- Export capabilities

---

## 💳 Payment Flow

1. **User selects product** → Redirected to checkout
2. **User enters email** → Chooses crypto (BTC/ETH/SOL/USDT)
3. **Click "Pay"** → API creates NOWPayments invoice
4. **Redirect to NOWPayments** → User completes crypto payment
5. **Webhook receives confirmation** → Order marked as confirmed
6. **Email sent automatically** → Contains download link
7. **User redirected to success page** → Can download product

---

## 🔒 Security Features

- Row Level Security (RLS) in Supabase
- Webhook signature verification
- Environment variable protection
- No PII stored in frontend
- Crypto payments (no credit card data)
- HTTPS required in production

---

## 🌐 Deployment (Vercel)

### 1. Push to GitHub
```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin your-repo-url
git push -u origin main
```

### 2. Deploy to Vercel
1. Go to [vercel.com](https://vercel.com)
2. Click "New Project"
3. Import your GitHub repo
4. Add environment variables from `.env`
5. Click "Deploy"

### 3. Configure NOWPayments
1. Login to NOWPayments dashboard
2. Set IPN callback URL: `https://your-domain.com/api/webhooks/nowpayments`
3. Copy IPN secret to env vars

### 4. Set up custom domain
1. In Vercel dashboard → Settings → Domains
2. Add your domain
3. Update DNS records

---

## 📧 Email Setup (Resend)

1. Sign up at [resend.com](https://resend.com)
2. Verify your domain
3. Create API key
4. Add to environment variables
5. Customize email template in webhook handler

---

## 🎯 Post-MVP Roadmap

### Phase 2 Features
- [ ] User authentication (Supabase Auth)
- [ ] Customer dashboard (purchase history)
- [ ] Download system with secure URLs
- [ ] Affiliate tracking system
- [ ] Coupon/discount codes
- [ ] Email sequences (abandoned cart)
- [ ] Advanced analytics
- [ ] A/B testing

### Phase 3 Features
- [ ] Community access (Discord integration)
- [ ] Content drip system
- [ ] Subscription products
- [ ] Multi-language support
- [ ] Advanced SEO optimization

---

## 💰 Cost Breakdown (Monthly)

| Service | Tier | Cost |
|---------|------|------|
| Vercel | Pro | $20 |
| Supabase | Pro | $25 |
| Resend | Free → Pro | $0-20 |
| NOWPayments | Transaction Fee | 0.5% |
| **Total** | | **~$45-65/mo** |

---

## 🎨 Design System

### Colors
```css
--base-black: #050505
--base-charcoal: #121214
--base-surface: #1A1A1C
--electric-purple: #8B5CF6
--electric-blue: #3B82F6
--text-primary: #EDEDED
--text-secondary: #A1A1AA
--text-muted: #71717A
```

### Typography
```css
font-family: 'Inter', system-ui, sans-serif
font-display: 'Inter Tight', 'Inter'
font-mono: 'JetBrains Mono', monospace
```

### Spacing
- Glass padding: 8px (cards) / 12px (strong)
- Section padding: 24px (mobile) / 96px (desktop)
- Grid gaps: 16px / 24px
- Border radius: 16px (cards) / 8px (buttons)

---

## 📝 Legal Compliance

**Educational Disclaimer Placement:**
1. Landing page footer (beautifully typeset)
2. Product cards (monospace labels)
3. Checkout page (small text)
4. Success page
5. Email templates

**Key Messages:**
- "Educational purposes only"
- "Not financial advice"
- "No guaranteed income claims"
- "Always do your own research"

---

## 🛠️ Development Commands

```bash
npm run dev      # Start development server
npm run build    # Build for production
npm run start    # Start production server
npm run lint     # Run ESLint
```

---

## 📞 Support

For issues or questions:
- Check Supabase logs for database errors
- Check Vercel logs for API errors
- Check NOWPayments dashboard for payment status
- Verify webhook signature matches

---

## 🎉 What's Included

✅ **Landing Page** - Premium Sovereign Architect design
✅ **Checkout Flow** - Complete crypto payment integration
✅ **Admin Dashboard** - Analytics and product management
✅ **Success Page** - Post-purchase confirmation
✅ **API Routes** - Payment creation + webhook handling
✅ **Database Schema** - Complete Supabase structure
✅ **Email Template** - Resend integration ready
✅ **Design System** - Tailwind config with brand colors
✅ **TypeScript** - Full type safety
✅ **Responsive** - Mobile-first design
✅ **Animations** - Framer Motion throughout

---

## 🚨 Important Notes

1. **Replace Mock Data**: All product data is currently mocked. Connect to Supabase in production.
2. **Admin Auth**: Add authentication check to admin routes before production.
3. **File Storage**: Set up Supabase Storage for PDF hosting.
4. **Email Testing**: Test email delivery thoroughly before launch.
5. **Payment Testing**: Use NOWPayments sandbox mode first.

---

Built with Next.js 14, Tailwind CSS, Framer Motion, and Supabase.
Design inspired by Linear, Stripe, and Apple's minimalism.

**License:** MIT
