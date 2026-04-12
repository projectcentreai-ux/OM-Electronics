# OM Electronics – Firebase Setup & Developer Guide

## 1. Firebase Project Setup

Your Firebase project is already configured:
- **Project ID:** `om-electronics-a6b0b`
- **Auth Domain:** `om-electronics-a6b0b.firebaseapp.com`

---

## 2. Enable Firebase Services

Go to [Firebase Console](https://console.firebase.google.com/project/om-electronics-a6b0b) and enable:

### Authentication
1. Firebase Console → **Authentication** → Get Started
2. Enable **Email/Password** sign-in method
3. Create your admin user: Authentication → Users → Add User
   - Email: `admin@omelectronics.in`
   - Set a strong password

### Firestore Database
1. Firebase Console → **Firestore Database** → Create Database
2. Choose **production mode** (security rules will be applied)
3. Select region: **asia-south1** (Mumbai) for best performance in India

### Deploy Firestore Security Rules
```bash
# Install Firebase CLI if not done
npm install -g firebase-tools

# Login
firebase login

# Initialize (from your project folder)
firebase init firestore

# Deploy rules
firebase deploy --only firestore:rules
```

Or paste `firestore.rules` content directly in the Firebase Console → Firestore → Rules tab.

---

## 3. Set Up Admin Access

After creating your admin user in Firebase Authentication:

1. Get the admin user's UID from Authentication → Users
2. In Firestore, create a collection: `admins`
3. Add a document with the admin's UID as document ID:
   ```json
   {
     "role": "admin",
     "email": "admin@omelectronics.in"
   }
   ```

Or update `firestore.rules` to hardcode your admin email:
```
request.auth.token.email == 'your-admin@email.com'
```

---

## 4. ImgBB Image Upload

Product images are uploaded via ImgBB API.
- **API Key:** `081139e431387df68a678be0c8b96ec6`
- Images are uploaded from the Admin Panel when adding/editing products
- URLs are stored in Firestore under each component's `imageUrl` field

---

## 5. Excel Import Format

Use the Admin Panel → **Import Excel** to bulk-upload components.

### Standard Format (recommended):
| Name | Category | Price | Description | Stock |
|------|----------|-------|-------------|-------|
| BC547 NPN Transistor | TRANSISTORS | 2.50 | General purpose NPN transistor | 500 |
| 10K Resistor 1/4W | RESISTORS | 0.50 | Metal film resistor 10K Ohm | 1000 |
| 100uF 16V Capacitor | CAPACITORS | 1.50 | Electrolytic capacitor 100uF 16V | 250 |

### Stock List Format (your existing file):
The app also auto-detects the stock list format with columns:
**Name | Alias | Parent Group | Op. Stock | Unit**

---

## 6. Firestore Data Structure

### components
```
componentId (auto)
├── name: "BC547 NPN Transistor"
├── category: "TRANSISTORS"
├── price: 2.50
├── description: "General purpose NPN transistor"
├── specifications: { "package": "TO-92", "voltage": "45V" }
├── imageUrl: "https://i.ibb.co/..."
├── stock: 500
├── enabled: true
└── createdAt: timestamp
```

### categories
```
categoryId (auto)
├── name: "TRANSISTORS"
├── enabled: true
└── createdAt: timestamp
```

### orders
```
orderId (auto)
├── userId: "firebase-auth-uid" or "guest"
├── items: [{ id, name, cat, price, qty }]
├── totalAmount: 125.00
├── status: "pending" | "processing" | "shipped" | "delivered" | "cancelled"
├── customerDetails: { name, email, phone, address }
└── createdAt: timestamp
```

### users
```
userId (from Firebase Auth UID)
├── name: "Customer Name"
├── email: "customer@email.com"
├── phone: "+91 98765 43210"
├── address: "123 Street, Chennai"
├── orderHistory: ["orderId1", "orderId2"]
└── createdAt: timestamp
```

---

## 7. File Structure

```
om-electronics/
├── index.html          # Homepage
├── shop.html           # Product listing & search
├── admin.html          # Admin panel
├── checkout.html       # Checkout page
├── firestore.rules     # Firestore security rules
└── SETUP.md            # This file
```

---

## 8. Quick Start Checklist

- [ ] Firebase project is live (om-electronics-a6b0b)
- [ ] Authentication enabled with Email/Password
- [ ] Admin user created in Firebase Auth
- [ ] Firestore database created (asia-south1)
- [ ] Security rules deployed from `firestore.rules`
- [ ] Admin document created in `admins` collection
- [ ] Categories added via Admin Panel
- [ ] Products added (manually or via Excel Import)
- [ ] Test the shop at `index.html`

---

## 9. Deployment

Host for free on Firebase Hosting:
```bash
npm install -g firebase-tools
firebase login
firebase init hosting
# Set public directory to: . (current folder)
# Single-page app: No
firebase deploy
```

Or upload all HTML files to any web hosting (Hostinger, cPanel, Netlify, etc.).

---

## Support

For Firebase issues: https://firebase.google.com/docs
For ImgBB API: https://api.imgbb.com/
