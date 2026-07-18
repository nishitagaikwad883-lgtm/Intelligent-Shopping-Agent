<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Smart Shopping Agent</title>
  <style>
    /* ── Reset & Base ─────────────────────────────── */
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: -apple-system, "Segoe UI", system-ui, sans-serif;
      background: #f0f4f8;
      color: #1f2328;
      min-height: 100vh;
    }

    /* ── Header ───────────────────────────────────── */
    header {
      background: #1e293b;
      color: #fff;
      padding: 0 24px;
      height: 60px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      position: sticky;
      top: 0;
      z-index: 100;
      box-shadow: 0 2px 8px rgba(0,0,0,0.25);
    }
    .logo { font-size: 1.25rem; font-weight: 700; letter-spacing: -0.5px; }
    .logo span { color: #38bdf8; }
    .cart-btn {
      background: #38bdf8;
      border: none;
      color: #fff;
      padding: 8px 18px;
      border-radius: 8px;
      font-size: 0.9rem;
      cursor: pointer;
      font-weight: 600;
      transition: background 0.2s;
      position: relative;
    }
    .cart-btn:hover { background: #0ea5e9; }
    #cart-count {
      background: #ef4444;
      color: #fff;
      border-radius: 50%;
      font-size: 0.72rem;
      font-weight: 700;
      width: 18px; height: 18px;
      display: inline-flex;
      align-items: center;
      justify-content: center;
      margin-left: 6px;
    }

    /* ── Layout ───────────────────────────────────── */
    .container { max-width: 1200px; margin: 0 auto; padding: 24px 16px; }

    /* ── Agent Chat Banner ────────────────────────── */
    .agent-section {
      background: #fff;
      border-radius: 16px;
      padding: 20px 24px;
      margin-bottom: 28px;
      box-shadow: 0 1px 4px rgba(0,0,0,0.08);
      border: 1px solid #e5e7eb;
    }
    .agent-header {
      display: flex;
      align-items: center;
      gap: 10px;
      margin-bottom: 14px;
    }
    .agent-avatar {
      width: 40px; height: 40px;
      background: #1e293b;
      border-radius: 50%;
      display: flex; align-items: center; justify-content: center;
      font-size: 1.2rem;
    }
    .agent-name { font-weight: 700; font-size: 1rem; }
    .agent-status { font-size: 0.78rem; color: #22c55e; }

    .chat-messages {
      min-height: 80px;
      max-height: 260px;
      overflow-y: auto;
      margin-bottom: 14px;
      display: flex;
      flex-direction: column;
      gap: 8px;
    }
    .msg {
      padding: 10px 14px;
      border-radius: 12px;
      font-size: 0.9rem;
      line-height: 1.5;
      max-width: 82%;
    }
    .msg.bot {
      background: #f0f9ff;
      border: 1px solid #bae6fd;
      color: #0c4a6e;
      align-self: flex-start;
    }
    .msg.user {
      background: #1e293b;
      color: #fff;
      align-self: flex-end;
    }
    .chat-input-row {
      display: flex;
      gap: 10px;
    }
    #user-input {
      flex: 1;
      padding: 10px 14px;
      border: 1.5px solid #e5e7eb;
      border-radius: 10px;
      font-size: 0.9rem;
      outline: none;
      transition: border-color 0.2s;
    }
    #user-input:focus { border-color: #38bdf8; }
    #send-btn {
      background: #1e293b;
      color: #fff;
      border: none;
      padding: 10px 20px;
      border-radius: 10px;
      font-size: 0.9rem;
      font-weight: 600;
      cursor: pointer;
      transition: background 0.2s;
    }
    #send-btn:hover { background: #334155; }

    /* ── Filters & Search ─────────────────────────── */
    .controls {
      display: flex;
      flex-wrap: wrap;
      gap: 12px;
      margin-bottom: 24px;
      align-items: center;
    }
    .search-wrap { flex: 1; min-width: 220px; position: relative; }
    .search-wrap input {
      width: 100%;
      padding: 10px 14px 10px 38px;
      border: 1.5px solid #e5e7eb;
      border-radius: 10px;
      font-size: 0.9rem;
      outline: none;
      background: #fff;
      transition: border-color 0.2s;
    }
    .search-wrap input:focus { border-color: #38bdf8; }
    .search-icon {
      position: absolute;
      left: 12px; top: 50%;
      transform: translateY(-50%);
      color: #9ca3af;
      font-size: 1rem;
    }
    select {
      padding: 10px 14px;
      border: 1.5px solid #e5e7eb;
      border-radius: 10px;
      font-size: 0.9rem;
      background: #fff;
      cursor: pointer;
      outline: none;
    }
    .price-filter { display: flex; align-items: center; gap: 8px; font-size: 0.9rem; }
    .price-filter input[type=range] { width: 120px; accent-color: #38bdf8; }

    /* ── Sort & Results Info ──────────────────────── */
    .results-bar {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 16px;
      font-size: 0.88rem;
      color: #57606a;
    }

    /* ── Product Grid ─────────────────────────────── */
    .product-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(230px, 1fr));
      gap: 20px;
    }
    .product-card {
      background: #fff;
      border-radius: 14px;
      border: 1px solid #e5e7eb;
      overflow: hidden;
      transition: box-shadow 0.2s, transform 0.2s;
      display: flex;
      flex-direction: column;
    }
    .product-card:hover {
      box-shadow: 0 6px 24px rgba(0,0,0,0.10);
      transform: translateY(-3px);
    }
    .product-img {
      width: 100%;
      height: 180px;
      object-fit: cover;
      background: #f7f8fa;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 3.5rem;
    }
    .product-body { padding: 14px; flex: 1; display: flex; flex-direction: column; gap: 6px; }
    .product-category {
      font-size: 0.72rem;
      text-transform: uppercase;
      letter-spacing: 0.5px;
      color: #7c5cd8;
      font-weight: 600;
    }
    .product-name { font-size: 0.95rem; font-weight: 700; line-height: 1.35; }
    .product-desc { font-size: 0.82rem; color: #57606a; line-height: 1.4; flex: 1; }
    .product-footer {
      display: flex;
      align-items: center;
      justify-content: space-between;
      margin-top: 10px;
    }
    .product-price { font-size: 1.1rem; font-weight: 700; color: #1e293b; }
    .product-old-price { font-size: 0.8rem; color: #9ca3af; text-decoration: line-through; }
    .rating { font-size: 0.82rem; color: #f59e0b; }
    .add-btn {
      background: #1e293b;
      color: #fff;
      border: none;
      padding: 7px 14px;
      border-radius: 8px;
      font-size: 0.82rem;
      font-weight: 600;
      cursor: pointer;
      transition: background 0.2s;
    }
    .add-btn:hover { background: #38bdf8; }
    .badge {
      display: inline-block;
      font-size: 0.7rem;
      padding: 2px 7px;
      border-radius: 20px;
      font-weight: 600;
    }
    .badge-sale { background: #fee2e2; color: #dc2626; }
    .badge-new  { background: #dcfce7; color: #16a34a; }
    .badge-hot  { background: #fef3c7; color: #d97706; }

    /* ── Cart Modal ───────────────────────────────── */
    .overlay {
      display: none;
      position: fixed; inset: 0;
      background: rgba(0,0,0,0.45);
      z-index: 200;
      align-items: flex-start;
      justify-content: flex-end;
    }
    .overlay.open { display: flex; }
    .cart-panel {
      background: #fff;
      width: 380px;
      max-width: 100vw;
      height: 100vh;
      overflow-y: auto;
      padding: 24px;
      display: flex;
      flex-direction: column;
      gap: 16px;
    }
    .cart-panel h2 { font-size: 1.2rem; font-weight: 700; }
    .cart-close {
      background: none;
      border: none;
      font-size: 1.4rem;
      cursor: pointer;
      color: #57606a;
    }
    .cart-item {
      display: flex;
      gap: 12px;
      align-items: center;
      padding: 12px;
      border-radius: 10px;
      background: #f7f8fa;
      border: 1px solid #e5e7eb;
    }
    .cart-item-icon { font-size: 2rem; }
    .cart-item-info { flex: 1; }
    .cart-item-name { font-size: 0.9rem; font-weight: 600; }
    .cart-item-price { font-size: 0.85rem; color: #57606a; }
    .qty-btn {
      background: #e5e7eb;
      border: none;
      width: 26px; height: 26px;
      border-radius: 6px;
      font-size: 1rem;
      cursor: pointer;
      font-weight: 700;
    }
    .qty-btn:hover { background: #38bdf8; color: #fff; }
    .qty-display { font-size: 0.9rem; font-weight: 700; min-width: 20px; text-align: center; }
    .remove-btn {
      background: none;
      border: none;
      color: #ef4444;
      font-size: 1.1rem;
      cursor: pointer;
    }
    .cart-summary {
      border-top: 2px solid #e5e7eb;
      padding-top: 16px;
      margin-top: auto;
    }
    .cart-total { font-size: 1.1rem; font-weight: 700; margin-bottom: 12px; }
    .checkout-btn {
      width: 100%;
      background: #38bdf8;
      border: none;
      color: #fff;
      padding: 14px;
      border-radius: 10px;
      font-size: 1rem;
      font-weight: 700;
      cursor: pointer;
      transition: background 0.2s;
    }
    .checkout-btn:hover { background: #0ea5e9; }
    .empty-cart { text-align: center; color: #9ca3af; font-size: 0.9rem; padding: 40px 0; }

    /* ── Recommended Tag ──────────────────────────── */
    .ai-tag {
      display: inline-block;
      background: #ede9fe;
      color: #7c5cd8;
      font-size: 0.7rem;
      padding: 2px 7px;
      border-radius: 20px;
      font-weight: 600;
      margin-left: 4px;
    }

    /* ── Toast ────────────────────────────────────── */
    #toast {
      position: fixed;
      bottom: 28px; left: 50%;
      transform: translateX(-50%) translateY(80px);
      background: #1e293b;
      color: #fff;
      padding: 11px 24px;
      border-radius: 10px;
      font-size: 0.9rem;
      font-weight: 600;
      z-index: 999;
      transition: transform 0.35s ease;
      pointer-events: none;
    }
    #toast.show { transform: translateX(-50%) translateY(0); }

    /* ── Responsive ───────────────────────────────── */
    @media (max-width: 600px) {
      .controls { flex-direction: column; }
      .cart-panel { width: 100vw; }
      .product-grid { grid-template-columns: repeat(auto-fill, minmax(160px, 1fr)); }
    }
  </style>
</head>
<body>

    <script>
  window.wxOConfiguration = {
    orchestrationID: "742cfbe48aa84e8e8c9d0d31bacbfd8f_6ad26a96-02bb-40c3-90e9-b11ac8ec7096",
    hostURL: "https://au-syd.watson-orchestrate.cloud.ibm.com",
    rootElementID: "root",
    deploymentPlatform: "ibmcloud",
    crn: "crn:v1:bluemix:public:watsonx-orchestrate:au-syd:a/742cfbe48aa84e8e8c9d0d31bacbfd8f:6ad26a96-02bb-40c3-90e9-b11ac8ec7096::",
    chatOptions: {
        agentId: "10c47bf3-4516-4e07-b48e-7c15e5d2bd2e", 
    }
  };
  setTimeout(function () {
    const script = document.createElement('script');
    script.src = `${window.wxOConfiguration.hostURL}/wxochat/wxoLoader.js?embed=true`;
    script.addEventListener('load', function () {
        wxoLoader.init();
    });
    document.head.appendChild(script);
  }, 0);                     
</script>

<!-- ── Header ──────────────────────────────────────── -->
<header>
  <div class="logo">Smart<span>Shop</span> AI</div>
  <button class="cart-btn" onclick="toggleCart()">
    🛒 Cart <span id="cart-count">0</span>
  </button>
</header>

<!-- ── Cart Overlay ─────────────────────────────────── -->
<div class="overlay" id="cart-overlay" onclick="handleOverlayClick(event)">
  <div class="cart-panel">
    <div style="display:flex;justify-content:space-between;align-items:center;">
      <h2>🛒 Your Cart</h2>
      <button class="cart-close" onclick="toggleCart()">✕</button>
    </div>
    <div id="cart-items-list">
      <div class="empty-cart">Your cart is empty.<br>Start adding products!</div>
    </div>
    <div class="cart-summary" id="cart-summary" style="display:none;">
      <div class="cart-total" id="cart-total-display">Total: $0.00</div>
      <button class="checkout-btn" onclick="checkout()">Checkout →</button>
    </div>
  </div>
</div>

<!-- ── Toast ─────────────────────────────────────────── -->
<div id="toast"></div>

<!-- ── Main Content ──────────────────────────────────── -->
<div class="container">

  <!-- Agent Chat -->
  <section class="agent-section">
    <div class="agent-header">
      <div class="agent-avatar">🤖</div>
      <div>
        <div class="agent-name">ShopBot AI</div>
        <div class="agent-status">● Online — ready to help</div>
      </div>
    </div>
    <div class="chat-messages" id="chat-messages">
      <div class="msg bot">
        Hi! I'm your Smart Shopping Agent. Tell me what you're looking for — I'll find the best products for you! Try: <strong>"Show me headphones under $100"</strong> or <strong>"Best laptop deals"</strong>.
      </div>
    </div>
    <div class="chat-input-row">
      <input id="user-input" type="text" placeholder="Ask me anything… e.g. 'I need a gift under $50'" />
      <button id="send-btn" onclick="sendMessage()">Send</button>
    </div>
  </section>

  <!-- Controls -->
  <div class="controls">
    <div class="search-wrap">
      <span class="search-icon">🔍</span>
      <input type="text" id="search-input" placeholder="Search products…" oninput="filterProducts()" />
    </div>
    <select id="cat-filter" onchange="filterProducts()">
      <option value="">All Categories</option>
      <option value="Electronics">Electronics</option>
      <option value="Audio">Audio</option>
      <option value="Wearables">Wearables</option>
      <option value="Home">Home</option>
      <option value="Gaming">Gaming</option>
      <option value="Fashion">Fashion</option>
    </select>
    <select id="sort-filter" onchange="filterProducts()">
      <option value="default">Sort: Default</option>
      <option value="price-asc">Price: Low → High</option>
      <option value="price-desc">Price: High → Low</option>
      <option value="rating">Top Rated</option>
    </select>
    <div class="price-filter">
      Max: <strong id="price-label">$500</strong>
      <input type="range" id="price-range" min="10" max="1000" value="500" step="10" oninput="updatePrice()" />
    </div>
  </div>

  <!-- Results bar -->
  <div class="results-bar">
    <span id="result-count">Showing all products</span>
    <span style="color:#7c5cd8;font-size:0.82rem;font-weight:600;">✦ AI-recommended items highlighted</span>
  </div>

  <!-- Product Grid -->
  <div class="product-grid" id="product-grid"></div>

</div>

<script>
/* ──────────────────────────────────────────────
   DATA
────────────────────────────────────────────── */
const PRODUCTS = [
  { id:1,  name:"Sony WH-1000XM5",        category:"Audio",       price:279,  oldPrice:349, rating:4.9, emoji:"🎧", desc:"Industry-leading noise cancelling headphones with 30hr battery.", badge:"hot",  ai:true  },
  { id:2,  name:"Apple AirPods Pro 2",     category:"Audio",       price:199,  oldPrice:249, rating:4.8, emoji:"🎵", desc:"Active noise cancellation, transparency mode, spatial audio.", badge:"sale", ai:false },
  { id:3,  name:"Samsung 65\" QLED TV",    category:"Electronics", price:899,  oldPrice:1199,rating:4.7, emoji:"📺", desc:"Quantum HDR, 120Hz, 4K Ultra HD smart TV.", badge:"sale", ai:false },
  { id:4,  name:"MacBook Air M3",          category:"Electronics", price:1099, oldPrice:null, rating:4.9, emoji:"💻", desc:"Apple M3 chip, 18hr battery, fanless design.", badge:"new",  ai:true  },
  { id:5,  name:"iPad Pro 11\"",           category:"Electronics", price:799,  oldPrice:899, rating:4.8, emoji:"📱", desc:"M4 chip, Ultra Retina XDR display, Apple Pencil Pro support.", badge:"new",  ai:false },
  { id:6,  name:"Apple Watch Series 10",  category:"Wearables",   price:399,  oldPrice:449, rating:4.7, emoji:"⌚", desc:"Advanced health sensors, crash detection, sleep tracking.", badge:"new",  ai:false },
  { id:7,  name:"Fitbit Charge 6",         category:"Wearables",   price:99,   oldPrice:149, rating:4.5, emoji:"🏃", desc:"Built-in GPS, heart rate tracking, 7-day battery life.", badge:"sale", ai:true  },
  { id:8,  name:"Dyson V15 Detect",        category:"Home",        price:649,  oldPrice:749, rating:4.8, emoji:"🧹", desc:"Laser dust detection, HEPA filtration, 60min run time.", badge:"hot",  ai:false },
  { id:9,  name:"Instant Pot Duo 7-in-1",  category:"Home",        price:79,   oldPrice:99,  rating:4.7, emoji:"🍲", desc:"Pressure cooker, slow cooker, rice cooker, steamer & more.", badge:"sale", ai:true  },
  { id:10, name:"PlayStation 5 Slim",      category:"Gaming",      price:449,  oldPrice:499, rating:4.9, emoji:"🎮", desc:"Ultra-high speed SSD, 4K gaming, DualSense controller.", badge:"hot",  ai:false },
  { id:11, name:"Nintendo Switch OLED",    category:"Gaming",      price:349,  oldPrice:null, rating:4.8, emoji:"🕹️", desc:"Vivid 7-inch OLED screen, TV and handheld modes.", badge:"",    ai:true  },
  { id:12, name:"Logitech MX Master 3S",   category:"Electronics", price:99,   oldPrice:119, rating:4.7, emoji:"🖱️", desc:"8000 DPI sensor, ultra-fast scroll, whisper-quiet clicks.", badge:"sale", ai:true  },
  { id:13, name:"Levi's 501 Original",     category:"Fashion",     price:59,   oldPrice:79,  rating:4.6, emoji:"👖", desc:"Classic straight fit, 100% cotton denim, timeless style.", badge:"sale", ai:false },
  { id:14, name:"Nike Air Max 270",        category:"Fashion",     price:119,  oldPrice:149, rating:4.7, emoji:"👟", desc:"Max Air unit, lightweight mesh upper, all-day comfort.", badge:"hot",  ai:true  },
  { id:15, name:"Anker 65W USB-C Charger", category:"Electronics", price:35,   oldPrice:45,  rating:4.8, emoji:"🔌", desc:"GaN technology, charges MacBook, iPad, iPhone simultaneously.", badge:"",    ai:true  },
  { id:16, name:"IKEA Smart Desk Lamp",    category:"Home",        price:39,   oldPrice:null, rating:4.4, emoji:"💡", desc:"Dimmable LED, USB charging port, touch controls.", badge:"new",  ai:false },
];

/* ──────────────────────────────────────────────
   STATE
────────────────────────────────────────────── */
let cart = {};          // { productId: quantity }
let maxPrice = 500;
let aiHighlightIds = new Set(PRODUCTS.filter(p => p.ai).map(p => p.id));

/* ──────────────────────────────────────────────
   RENDER PRODUCTS
────────────────────────────────────────────── */
function renderProducts(list) {
  const grid = document.getElementById("product-grid");
  grid.innerHTML = "";
  if (list.length === 0) {
    grid.innerHTML = `<div style="grid-column:1/-1;text-align:center;padding:60px;color:#9ca3af;">No products found. Try a different search!</div>`;
    document.getElementById("result-count").textContent = "0 products found";
    return;
  }
  document.getElementById("result-count").textContent = `Showing ${list.length} product${list.length !== 1 ? "s" : ""}`;
  list.forEach(p => {
    const card = document.createElement("div");
    card.className = "product-card";
    const stars = "★".repeat(Math.round(p.rating)) + "☆".repeat(5 - Math.round(p.rating));
    const aiTag = aiHighlightIds.has(p.id) ? `<span class="ai-tag">✦ AI Pick</span>` : "";
    const badgeHtml = p.badge ? `<span class="badge badge-${p.badge}">${p.badge.toUpperCase()}</span>` : "";
    const oldPrice = p.oldPrice ? `<span class="product-old-price">$${p.oldPrice}</span>` : "";
    card.innerHTML = `
      <div class="product-img">${p.emoji}</div>
      <div class="product-body">
        <div style="display:flex;gap:5px;align-items:center;flex-wrap:wrap;">
          <span class="product-category">${p.category}</span>
          ${badgeHtml}
          ${aiTag}
        </div>
        <div class="product-name">${p.name}</div>
        <div class="product-desc">${p.desc}</div>
        <div style="display:flex;align-items:center;gap:6px;flex-wrap:wrap;">
          <span class="rating">${stars}</span>
          <span style="font-size:0.78rem;color:#57606a;">${p.rating}/5</span>
        </div>
        <div class="product-footer">
          <div>
            <div class="product-price">$${p.price}</div>
            ${oldPrice}
          </div>
          <button class="add-btn" onclick="addToCart(${p.id})">+ Add</button>
        </div>
      </div>`;
    grid.appendChild(card);
  });
}

/* ──────────────────────────────────────────────
   FILTER & SORT
────────────────────────────────────────────── */
function filterProducts() {
  const query   = document.getElementById("search-input").value.toLowerCase().trim();
  const cat     = document.getElementById("cat-filter").value;
  const sort    = document.getElementById("sort-filter").value;

  let list = PRODUCTS.filter(p => {
    const matchQ   = !query || p.name.toLowerCase().includes(query) || p.desc.toLowerCase().includes(query) || p.category.toLowerCase().includes(query);
    const matchCat = !cat   || p.category === cat;
    const matchPr  = p.price <= maxPrice;
    return matchQ && matchCat && matchPr;
  });

  if (sort === "price-asc")  list.sort((a,b) => a.price - b.price);
  if (sort === "price-desc") list.sort((a,b) => b.price - a.price);
  if (sort === "rating")     list.sort((a,b) => b.rating - a.rating);

  renderProducts(list);
}

function updatePrice() {
  maxPrice = parseInt(document.getElementById("price-range").value);
  document.getElementById("price-label").textContent = `$${maxPrice}`;
  filterProducts();
}

/* ──────────────────────────────────────────────
   CART
────────────────────────────────────────────── */
function addToCart(id) {
  cart[id] = (cart[id] || 0) + 1;
  updateCartUI();
  const p = PRODUCTS.find(x => x.id === id);
  showToast(`${p.emoji} "${p.name}" added to cart!`);
}

function changeQty(id, delta) {
  if (!cart[id]) return;
  cart[id] += delta;
  if (cart[id] <= 0) delete cart[id];
  updateCartUI();
  renderCartPanel();
}

function removeFromCart(id) {
  delete cart[id];
  updateCartUI();
  renderCartPanel();
}

function updateCartUI() {
  const total = Object.values(cart).reduce((a,b) => a+b, 0);
  document.getElementById("cart-count").textContent = total;
}

function renderCartPanel() {
  const list = document.getElementById("cart-items-list");
  const summary = document.getElementById("cart-summary");
  const keys = Object.keys(cart);
  if (keys.length === 0) {
    list.innerHTML = `<div class="empty-cart">Your cart is empty.<br>Start adding products!</div>`;
    summary.style.display = "none";
    return;
  }
  summary.style.display = "block";
  let total = 0;
  list.innerHTML = keys.map(id => {
    const p = PRODUCTS.find(x => x.id == id);
    const subtotal = p.price * cart[id];
    total += subtotal;
    return `
      <div class="cart-item">
        <div class="cart-item-icon">${p.emoji}</div>
        <div class="cart-item-info">
          <div class="cart-item-name">${p.name}</div>
          <div class="cart-item-price">$${p.price} × ${cart[id]} = <strong>$${subtotal.toFixed(2)}</strong></div>
        </div>
        <button class="qty-btn" onclick="changeQty(${id},-1)">−</button>
        <span class="qty-display">${cart[id]}</span>
        <button class="qty-btn" onclick="changeQty(${id},1)">+</button>
        <button class="remove-btn" onclick="removeFromCart(${id})">🗑</button>
      </div>`;
  }).join("");
  document.getElementById("cart-total-display").textContent = `Total: $${total.toFixed(2)}`;
}

function toggleCart() {
  const overlay = document.getElementById("cart-overlay");
  const isOpen = overlay.classList.contains("open");
  if (!isOpen) renderCartPanel();
  overlay.classList.toggle("open");
}

function handleOverlayClick(e) {
  if (e.target === document.getElementById("cart-overlay")) toggleCart();
}

function checkout() {
  cart = {};
  updateCartUI();
  renderCartPanel();
  toggleCart();
  showToast("🎉 Order placed! Thank you for shopping with SmartShop AI.");
}

/* ──────────────────────────────────────────────
   TOAST
────────────────────────────────────────────── */
let toastTimer;
function showToast(msg) {
  const t = document.getElementById("toast");
  t.textContent = msg;
  t.classList.add("show");
  clearTimeout(toastTimer);
  toastTimer = setTimeout(() => t.classList.remove("show"), 2800);
}

/* ──────────────────────────────────────────────
   AI AGENT CHAT
────────────────────────────────────────────── */
const agentResponses = [
  { keywords: ["headphone","earphone","audio","music","sound"],  cat:"Audio",       msg: "🎧 Here are the best audio products I found for you! The Sony WH-1000XM5 is my top pick — it has class-leading noise cancellation." },
  { keywords: ["laptop","macbook","computer","pc","notebook"],   cat:"Electronics", msg: "💻 Great choice! The MacBook Air M3 is an amazing option — blazing fast with incredible battery life." },
  { keywords: ["watch","fitness","health","workout","wearable"], cat:"Wearables",   msg: "⌚ For fitness & health tracking, I recommend checking out the Apple Watch Series 10 or the Fitbit Charge 6 for budget-friendly option." },
  { keywords: ["tv","television","screen","display"],            cat:"Electronics", msg: "📺 I found some great TV deals! The Samsung QLED offers stunning picture quality." },
  { keywords: ["gaming","game","console","play","ps5","xbox"],   cat:"Gaming",      msg: "🎮 Let's go! I've filtered the best gaming products for you. The PS5 Slim is a must-have!" },
  { keywords: ["home","kitchen","clean","vacuum","cook"],        cat:"Home",        msg: "🏠 Here are top home products! The Dyson V15 Detect is a premium vacuum, and the Instant Pot is perfect for easy cooking." },
  { keywords: ["fashion","clothes","shoes","shirt","jeans"],     cat:"Fashion",     msg: "👟 Styled and ready! Check out our top fashion picks below." },
  { keywords: ["cheap","budget","under","affordable","deal"],    cat:"",            msg: "💰 Looking for deals? I've sorted products by price for you — great value items coming up!", sort:"price-asc" },
  { keywords: ["best","top","popular","recommend"],              cat:"",            msg: "✦ Here are the highest-rated products I recommend for you!", sort:"rating" },
  { keywords: ["gift","present","birthday","surprise"],          cat:"",            msg: "🎁 Gift shopping? I'll show you the most popular items — perfect for any occasion!", sort:"rating" },
];

function sendMessage() {
  const input = document.getElementById("user-input");
  const text = input.value.trim();
  if (!text) return;
  appendMsg(text, "user");
  input.value = "";
  setTimeout(() => processAgent(text), 600);
}

function appendMsg(text, role) {
  const div = document.createElement("div");
  div.className = `msg ${role}`;
  div.innerHTML = text;
  const container = document.getElementById("chat-messages");
  container.appendChild(div);
  container.scrollTop = container.scrollHeight;
}

function processAgent(text) {
  const lower = text.toLowerCase();
  let matched = null;
  for (const r of agentResponses) {
    if (r.keywords.some(k => lower.includes(k))) { matched = r; break; }
  }

  if (matched) {
    appendMsg(matched.msg, "bot");
    // Apply filter
    if (matched.cat) {
      document.getElementById("cat-filter").value = matched.cat;
    } else {
      document.getElementById("cat-filter").value = "";
    }
    if (matched.sort) {
      document.getElementById("sort-filter").value = matched.sort;
    }
    document.getElementById("search-input").value = "";
    filterProducts();
  } else {
    // Fallback: search keyword
    appendMsg(`🔍 Searching for <strong>"${text}"</strong> across all products…`, "bot");
    document.getElementById("search-input").value = text;
    document.getElementById("cat-filter").value = "";
    filterProducts();
    setTimeout(() => {
      const count = document.getElementById("result-count").textContent;
      if (count.startsWith("0")) {
        appendMsg("😕 I couldn't find exact matches. Try broader terms like \"electronics\" or \"audio\".", "bot");
      } else {
        appendMsg(`✅ Found some results for you! Scroll down to explore.`, "bot");
      }
    }, 400);
  }
}

// Send on Enter
document.getElementById("user-input").addEventListener("keydown", e => {
  if (e.key === "Enter") sendMessage();
});

/* ──────────────────────────────────────────────
   INIT
────────────────────────────────────────────── */
filterProducts();
</script>
</body>
</html>
# Intelligent-Shopping-Agent
