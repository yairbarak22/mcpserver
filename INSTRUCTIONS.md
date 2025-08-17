# 🌱 Stevia Store MCP Server - הנחיות פיתוח

## 🎯 מטרת הפרויקט
בניית שרת MCP (Model Context Protocol) לחנות מוצרי סטיוויה של אורגניטל, שיעבוד עם Claude Remote Connectors.

## 📚 מקורות מידע רשמיים
- **MCP Official Documentation**: https://modelcontextprotocol.io/
- **MCP Server Quickstart**: https://modelcontextprotocol.io/quickstart/server
- **MCP SDK GitHub**: https://github.com/modelcontextprotocol/typescript-sdk
- **Claude MCP Integration**: https://docs.anthropic.com/en/docs/claude-code/mcp

## 🛠️ דרישות טכניות

### Stack Technology
- **TypeScript/JavaScript** - השפה הראשית
- **MCP SDK** - החבילה הרשמית של Anthropic
- **PostgreSQL** - מסד נתונים (עם fallback למידע mock)
- **Railway** - פלטפורמת deployment

### Transport Types Support
צריך לבנות שרת שתומך ב:
1. **stdio** - לפיתוח מקומי
2. **HTTP** - לRemote connections עם Claude
3. **SSE** - Server-Sent Events לreal-time

## 🏪 תכונות עסקיות נדרשות

### מוצרי הסטיוויה
- **get_products** - רשימת כל המוצרים
- **search_products** - חיפוש לפי מילות מפתח  
- **get_product_details** - פרטים על מוצר ספציפי

### עגלת קניות
- **create_shopping_cart** - יצירת עגלה חדשה
- **add_to_cart** - הוספת מוצרים לעגלה
- **get_cart_contents** - תוכן עגלה וחישוב סיכום

### ייעוץ בריאותי  
- **stevia_health_consultation** - ייעוץ לפי מצב רפואי
- **calculate_stevia_dosage** - חישוב מינון לפי משקל ומטרה

## 🗄️ מבנה מסד הנתונים

### Tables Required
```sql
-- מוצרים
CREATE TABLE products (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    sale_price DECIMAL(10,2),
    stock_quantity INTEGER DEFAULT 0,
    category VARCHAR(100),
    created_at TIMESTAMP DEFAULT NOW()
);

-- עגלות קניות
CREATE TABLE shopping_carts (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    session_id VARCHAR(255),
    created_at TIMESTAMP DEFAULT NOW()
);

-- פריטים בעגלה
CREATE TABLE cart_items (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    cart_id UUID REFERENCES shopping_carts(id) ON DELETE CASCADE,
    product_id UUID REFERENCES products(id),
    quantity INTEGER NOT NULL DEFAULT 1,
    created_at TIMESTAMP DEFAULT NOW()
);
```

### Sample Products Data
```typescript
const sampleProducts = [
    {
        name: 'סטיביאף אבקה טבעית',
        description: 'ממתיק טבעי מצמח הסטיוויה, נטול קלוריות לאיזון רמת הסוכר',
        price: 29.90,
        sale_price: 24.90,
        stock_quantity: 100,
        category: 'סטיוויה'
    },
    {
        name: 'סטיוויה נוזלית',
        description: 'ממתיק נוזלי מסטיוויה לשתייה חמה וקרה', 
        price: 35.00,
        sale_price: 29.90,
        stock_quantity: 50,
        category: 'סטיוויה'
    },
    {
        name: 'חבילת סטיוויה משפחתית',
        description: '3 יחידות סטיוויה במחיר מיוחד לכל המשפחה',
        price: 89.90,
        sale_price: 69.90,
        stock_quantity: 25,
        category: 'חבילות'
    }
];
```

## 🚀 מבנה פרויקט מומלץ

```
stevia-store-mcp/
├── package.json
├── tsconfig.json
├── src/
│   ├── index.ts              # Entry point
│   ├── server.ts             # MCP Server class
│   ├── database/
│   │   └── database.ts       # Database operations
│   ├── tools/
│   │   ├── products.ts       # Product tools
│   │   ├── cart.ts           # Cart tools
│   │   └── health.ts         # Health consultation tools
│   └── types/
│       └── index.ts          # TypeScript types
├── README.md
└── nixpacks.toml            # Railway config
```

## ⚙️ Environment Variables
```bash
# Railway
DATABASE_URL=postgresql://user:pass@host:port/db
PORT=3000
RAILWAY_PUBLIC_DOMAIN=your-domain.railway.app

# Local Development  
DB_HOST=localhost
DB_PORT=5432
DB_NAME=stevia_store
DB_USER=postgres
DB_PASSWORD=password
```

## 🔧 Package.json Template
```json
{
  "name": "stevia-store-mcp",
  "version": "1.0.0",
  "type": "module",
  "main": "dist/index.js",
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js",
    "dev": "tsx watch src/index.ts"
  },
  "dependencies": {
    "@modelcontextprotocol/sdk": "latest",
    "pg": "^8.12.0",
    "uuid": "^10.0.0",
    "zod": "^3.23.8"
  },
  "devDependencies": {
    "@types/node": "^22.0.0",
    "@types/pg": "^8.11.10",
    "@types/uuid": "^10.0.0", 
    "tsx": "^4.16.0",
    "typescript": "^5.5.0"
  }
}
```

## 🌐 Railway Deployment

### צעדים:
1. צור פרויקט Railway חדש
2. הוסף PostgreSQL service
3. חבר את הגיט repository
4. Railway יפרוס אוטומטית

### קבצים נדרשים:
- `nixpacks.toml` - הגדרות build
- `package.json` - dependencies ו-scripts
- Environment variables יוגדרו אוטומטית

## 🎨 UI/UX דרישות
- תמיכה מלאה בעברית
- הודעות ברורות למשתמש
- emoji icons לקטגוריות שונות
- מחירים בשקלים (₪)
- תאריכים בפורמט ישראלי

## 🛡️ אבטחה
- Input validation עם Zod
- SQL injection protection
- Environment variables לסודות
- Error handling graceful

## 📝 הערות פיתוח
1. התחל עם stdio transport ואחר כך הוסף HTTP
2. השתמש ב-mock data כ-fallback
3. בדוק שהכלים עובדים לפני deployment
4. תעד את ה-API בקובץ README

---

**🎯 המטרה: שרת MCP שעובד עם Claude Remote ומספק חוויית קנייה מלאה לחנות הסטיוויה של אורגניטל.**