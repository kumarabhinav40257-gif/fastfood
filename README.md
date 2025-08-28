# fastfoodimport React, { useMemo, useState, useEffect } from "react"; import { ShoppingCart, Search, MapPin, Clock, Bike, User, Plus, Minus, CheckCircle2, X, ArrowLeft, Phone, Gift, Star, Wallet } from "lucide-react";

/**

FastBite – Zomato se Sasta (Frontend Mock)

Single-file React app (no backend) to demo flows: Home → Product → Cart → Checkout → Tracking → Offers → Profile

Styling: TailwindCSS

Icons: lucide-react

Notes:

Data is mocked in-memory


Prices are INR


Designed for mobile viewport first (but responsive) */



// --- Mock Data -------------------------------------------------------------- const CATEGORIES = [ { id: "momos", name: "Momos", emoji: "🥟" }, { id: "burger", name: "Burger", emoji: "🍔" }, { id: "pizza", name: "Pizza", emoji: "🍕" }, { id: "rolls", name: "Rolls", emoji: "🌯" }, { id: "combos", name: "Combos", emoji: "🎁" }, ];

const MENU = [ { id: "momo-veg", title: "Veg Steam Momos", category: "momos", price: 70, compareAt: 120, rating: 4.5, img: "https://images.unsplash.com/photo-1651705721371-3b1a3f94a639?q=80&w=1200&auto=format&fit=crop", addons: [ { id: "ac1", name: "Extra Chutney", price: 5 }, { id: "ac2", name: "Mayo Dip", price: 10 }, { id: "ac3", name: "Cheese", price: 15 }, ], }, { id: "momo-paneer", title: "Paneer Momos", category: "momos", price: 90, compareAt: 150, rating: 4.6, img: "https://images.unsplash.com/photo-1625944525739-8e6c1e9a99e7?q=80&w=1200&auto=format&fit=crop", addons: [ { id: "ap1", name: "Tandoori Sauce", price: 15 }, { id: "ap2", name: "Cheese", price: 15 }, ], }, { id: "burger-aloo", title: "Aloo Tikki Burger", category: "burger", price: 50, compareAt: 90, rating: 4.3, img: "https://images.unsplash.com/photo-1550547660-d9450f859349?q=80&w=1200&auto=format&fit=crop", addons: [ { id: "ba1", name: "Extra Patty", price: 20 }, { id: "ba2", name: "Cheese Slice", price: 12 }, ], }, { id: "burger-paneer", title: "Paneer Burger", category: "burger", price: 80, compareAt: 140, rating: 4.4, img: "https://images.unsplash.com/photo-1544025162-d76694265947?q=80&w=1200&auto=format&fit=crop", addons: [ { id: "bp1", name: "Extra Cheese", price: 15 }, ], }, { id: "pizza-mini", title: "Mini Cheese Pizza", category: "pizza", price: 150, compareAt: 220, rating: 4.2, img: "https://images.unsplash.com/photo-1548365328-5473d2c8d6f7?q=80&w=1200&auto=format&fit=crop", addons: [ { id: "pa1", name: "Extra Cheese", price: 20 }, { id: "pa2", name: "Olives", price: 15 }, ], }, { id: "roll-paneer", title: "Paneer Roll", category: "rolls", price: 70, compareAt: 110, rating: 4.1, img: "https://images.unsplash.com/photo-1630307304746-5b3bfa2a343f?q=80&w=1200&auto=format&fit=crop", addons: [ { id: "ra1", name: "Mint Mayo", price: 10 }, ], }, { id: "combo-99", title: "₹99 Student Combo", subtitle: "Aloo Burger + Lemonade", category: "combos", price: 99, compareAt: 159, rating: 4.5, img: "https://images.unsplash.com/photo-1602333863800-123f403f6b63?q=80&w=1200&auto=format&fit=crop", addons: [], }, { id: "combo-149", title: "₹149 Momos + Coke", subtitle: "Veg Momos (8 pc) + 200ml", category: "combos", price: 149, compareAt: 219, rating: 4.6, img: "https://images.unsplash.com/photo-1604908554027-9b4f84cae353?q=80&w=1200&auto=format&fit=crop", addons: [], }, ];

// --- Helper utils ----------------------------------------------------------- const inr = (n) => ₹${n};

function classNames(...arr) { return arr.filter(Boolean).join(" "); }

// --- Components ------------------------------------------------------------- function TopBar({ title, onBack, right }) { return ( <div className="sticky top-0 z-30 bg-white/80 backdrop-blur border-b border-neutral-200"> <div className="max-w-md mx-auto flex items-center gap-2 p-3"> {onBack ? ( <button onClick={onBack} className="p-2 rounded-xl hover:bg-neutral-100"> <ArrowLeft size={20} /> </button> ) : ( <div className="w-10" /> )} <h1 className="font-extrabold text-lg tracking-tight">{title}</h1> <div className="ml-auto flex items-center gap-2">{right}</div> </div> </div> ); }

function Pill({ children, active, onClick }) { return ( <button onClick={onClick} className={classNames( "px-3 py-2 rounded-2xl text-sm font-semibold border", active ? "bg-orange-500 text-white border-orange-500" : "bg-white text-neutral-700 border-neutral-200 hover:bg-neutral-50" )} > {children} </button> ); }

function Qty({ value, onChange, size = "md" }) { const cls = size === "lg" ? "h-10 w-10" : "h-8 w-8"; return ( <div className="flex items-center gap-2"> <button className={classNames("grid place-content-center rounded-xl border", cls, "hover:bg-neutral-50")} onClick={() => onChange(Math.max(0, value - 1))}> <Minus size={18} /> </button> <span className="min-w-[1.5rem] text-center font-bold">{value}</span> <button className={classNames("grid place-content-center rounded-xl border", cls, "hover:bg-neutral-50")} onClick={() => onChange(value + 1)}> <Plus size={18} /> </button> </div> ); }

function ProductCard({ item, onOpen }) { return ( <div className="rounded-2xl bg-white border border-neutral-200 shadow-sm overflow-hidden"> <img src={item.img} alt={item.title} className="h-36 w-full object-cover" /> <div className="p-3"> <div className="flex items-center gap-1 text-sm text-yellow-600"><Star size={16} /> {item.rating} <span className="ml-auto text-xs text-neutral-500 line-through">{inr(item.compareAt)}</span> </div> <h3 className="font-bold mt-1 leading-tight">{item.title}</h3> {item.subtitle && <p className="text-xs text-neutral-600">{item.subtitle}</p>} <div className="mt-2 flex items-center justify-between"> <div className="text-lg font-extrabold text-orange-600">{inr(item.price)}</div> <button onClick={() => onOpen(item)} className="px-3 py-2 rounded-xl bg-orange-500 text-white font-semibold"> Add </button> </div> <p className="mt-1 text-[11px] text-neutral-500">~ {Math.round((1 - item.price / item.compareAt) * 100)}% cheaper than Zomato</p> </div> </div> ); }

function BottomBar({ cartCount, onCart, onOffers, onProfile, active = "home" }) { const Item = ({ id, icon, label, onClick }) => ( <button onClick={onClick} className={classNames("flex-1 grid place-items-center py-2", active === id ? "text-orange-600" : "text-neutral-600")}> {icon} <span className="text-[11px] font-semibold">{label}</span> </button> ); return ( <div className="sticky bottom-0 z-30 border-t border-neutral-200 bg-white/80 backdrop-blur"> <div className="max-w-md mx-auto grid grid-cols-4"> <Item id="home" icon={<Search size={20} />} label="Explore" onClick={() => window.scrollTo({ top: 0, behavior: "smooth" })} /> <Item id="offers" icon={<Gift size={20} />} label="Offers" onClick={onOffers} /> <Item id="profile" icon={<User size={20} />} label="Profile" onClick={onProfile} /> <button onClick={onCart} className="relative flex-1 grid place-items-center py-2 text-neutral-800"> <ShoppingCart size={20} /> <span className="text-[11px] font-semibold">Cart</span> {cartCount > 0 && ( <span className="absolute -top-1.5 right-6 min-w-[18px] h-[18px] grid place-items-center rounded-full bg-orange-600 text-white text-[10px] px-1"> {cartCount} </span> )} </button> </div> </div> ); }

// --- Root App --------------------------------------------------------------- export default function App() { const [view, setView] = useState("home"); const [activeCat, setActiveCat] = useState("momos"); const [query, setQuery] = useState(""); const [sheetItem, setSheetItem] = useState(null); const [cart, setCart] = useState([]); // {id, qty, addonIds: []} const [address, setAddress] = useState({ label: "Hostel A, Patna University", lat: 0, lng: 0 }); const [orderPlaced, setOrderPlaced] = useState(null); // order object

// Filtered menu const filtered = useMemo(() => MENU.filter(m => (activeCat === "all" || m.category === activeCat) && m.title.toLowerCase().includes(query.toLowerCase())), [activeCat, query]);

const cartTotal = useMemo(() => { return cart.reduce((sum, line) => { const prod = MENU.find(m => m.id === line.id); const addons = (prod?.addons || []).filter(a => line.addonIds?.includes(a.id)); const addonTotal = addons.reduce((s, a) => s + a.price, 0); return sum + (prod.price + addonTotal) * line.qty; }, 0); }, [cart]);

const deliveryFee = useMemo(() => (cartTotal > 0 && cartTotal < 199 ? 19 : 0), [cartTotal]);

function addToCart(product, qty = 1, addonIds = []) { setCart(prev => { const idx = prev.findIndex(l => l.id === product.id && JSON.stringify(l.addonIds || []) === JSON.stringify(addonIds || [])); if (idx >= 0) { const copy = [...prev]; copy[idx] = { ...copy[idx], qty: copy[idx].qty + qty }; return copy; } return [...prev, { id: product.id, qty, addonIds }]; }); } function updateLine(lineIndex, next) { setCart(prev => prev.map((l, i) => (i === lineIndex ? { ...l, ...next } : l)).filter(l => l.qty > 0)); } function clearCart() { setCart([]); }

// Fake order tracker useEffect(() => { if (!orderPlaced) return; const t1 = setTimeout(() => setOrderPlaced(o => ({ ...o, status: "preparing" })), 1500); const t2 = setTimeout(() => setOrderPlaced(o => ({ ...o, status: "out" })), 5500); const t3 = setTimeout(() => setOrderPlaced(o => ({ ...o, status: "delivered" })), 11000); return () => { clearTimeout(t1); clearTimeout(t2); clearTimeout(t3); }; }, [orderPlaced?.id]);

// --- Views ---------------------------------------------------------------- const Home = ( <div className="max-w-md mx-auto pb-24"> <TopBar title="FastBite – Zomato se Sasta" right={ <div className="flex items-center gap-2"> <MapPin size={18} className="text-orange-600" /> <span className="text-xs font-semibold text-neutral-700 truncate max-w-[160px]">{address.label}</span> </div> } />

{/* Search */}
  <div className="max-w-md mx-auto p-3">
    <div className="flex items-center gap-2 p-2 rounded-2xl border bg-white">
      <Search size={18} className="text-neutral-500" />
      <input value={query} onChange={(e) => setQuery(e.target.value)} placeholder="Find momos, burger, pizza…" className="flex-1 outline-none text-sm" />
    </div>
  </div>

  {/* Categories */}
  <div className="max-w-md mx-auto px-3 flex gap-2 overflow-x-auto no-scrollbar pb-2">
    <Pill active={activeCat === "all"} onClick={() => setActiveCat("all")}>All</Pill>
    {CATEGORIES.map(c => (
      <Pill key={c.id} active={activeCat === c.id} onClick={() => setActiveCat(c.id)}>
        <span className="mr-1">{c.emoji}</span>{c.name}
      </Pill>
    ))}
  </div>

  {/* Promo Banner */}
  <div className="max-w-md mx-auto px-3 mt-3">
    <div className="rounded-3xl p-4 bg-gradient-to-r from-orange-500 to-amber-500 text-white shadow-lg">
      <div className="text-xs font-semibold uppercase tracking-wider">Intro Offer</div>
      <div className="text-xl font-extrabold">₹99 Student Combo</div>
      <div className="text-sm opacity-90">Burger + Lemonade · Free delivery ≤ 3 km</div>
    </div>
  </div>

  {/* Grid */}
  <div className="max-w-md mx-auto p-3 grid grid-cols-2 gap-3 mt-2">
    {filtered.map((item) => (
      <ProductCard key={item.id} item={item} onOpen={setSheetItem} />
    ))}
  </div>

  <BottomBar
    cartCount={cart.reduce((s, l) => s + l.qty, 0)}
    onCart={() => setView("cart")}
    onOffers={() => setView("offers")}
    onProfile={() => setView("profile")}
    active="home"
  />

  {/* Product Sheet */}
  {sheetItem && (
    <div className="fixed inset-0 z-40 bg-black/40 flex items-end" onClick={() => setSheetItem(null)}>
      <div className="w-full max-w-md mx-auto bg-white rounded-t-3xl overflow-hidden" onClick={(e) => e.stopPropagation()}>
        <div className="relative">
          <img src={sheetItem.img} alt={sheetItem.title} className="h-48 w-full object-cover" />
          <button className="absolute top-3 right-3 p-2 bg-white/90 rounded-xl" onClick={() => setSheetItem(null)}><X size={18} /></button>
        </div>
        <div className="p-4">
          <h3 className="font-extrabold text-lg">{sheetItem.title}</h3>
          {sheetItem.subtitle && <p className="text-sm text-neutral-600">{sheetItem.subtitle}</p>}
          <div className="mt-1 flex items-center gap-2 text-sm text-yellow-700"><Star size={16} />{sheetItem.rating}
            <span className="ml-auto text-xs text-neutral-500 line-through">{inr(sheetItem.compareAt)}</span>
          </div>
          <div className="mt-3">
            <div className="text-sm font-semibold mb-2">Add-ons</div>
            {sheetItem.addons?.length ? (
              <AddonSelector item={sheetItem} />
            ) : (
              <div className="text-xs text-neutral-500">No add-ons available.</div>
            )}
          </div>
          <div className="mt-4 flex items-center justify-between">
            <div className="text-2xl font-extrabold text-orange-600">{inr(sheetItem.price)}</div>
            <button
              onClick={() => { addToCart(sheetItem, 1, selectedAddonsRef.current); setSheetItem(null); }}
              className="px-4 py-3 rounded-2xl bg-orange-600 text-white font-bold"
            >
              Add to Cart
            </button>
          </div>
          <p className="mt-1 text-[11px] text-neutral-500">~ {Math.round((1 - sheetItem.price / sheetItem.compareAt) * 100)}% cheaper than Zomato</p>
        </div>
      </div>
    </div>
  )}
</div>

);

function CartLine({ line, index }) { const prod = MENU.find(m => m.id === line.id); const addons = (prod.addons || []).filter(a => line.addonIds?.includes(a.id)); const addonTotal = addons.reduce((s, a) => s + a.price, 0); return ( <div className="flex items-start gap-3 p-3 border-b"> <img src={prod.img} className="h-16 w-16 object-cover rounded-xl" /> <div className="flex-1"> <div className="font-semibold leading-tight">{prod.title}</div> {addons.length > 0 && ( <div className="text-xs text-neutral-600">Add-ons: {addons.map(a => a.name).join(", ")}</div> )} <div className="mt-2 flex items-center justify-between"> <Qty value={line.qty} onChange={(q) => updateLine(index, { qty: q })} /> <div className="font-bold">{inr((prod.price + addonTotal) * line.qty)}</div> </div> </div> </div> ); }

const CartView = ( <div className="max-w-md mx-auto pb-24"> <TopBar title="Your Cart" onBack={() => setView("home")} right={<Clock size={18} />} /> {cart.length === 0 ? ( <div className="p-6 text-center text-neutral-600"> Cart is empty. Add some tasty bites! 🥟🍔🍕 </div> ) : ( <div className="divide-y"> {cart.map((line, idx) => ( <CartLine key={idx} line={line} index={idx} /> ))} </div> )}

<div className="fixed bottom-16 left-0 right-0">
    <div className="max-w-md mx-auto p-3">
      <div className="rounded-2xl border bg-white shadow-lg p-3">
        <div className="flex items-center justify-between text-sm">
          <span>Subtotal</span>
          <span className="font-semibold">{inr(cartTotal)}</span>
        </div>
        <div className="flex items-center justify-between text-sm mt-1">
          <span>Delivery</span>
          <span className="font-semibold">{deliveryFee === 0 ? "FREE" : inr(deliveryFee)}</span>
        </div>
        <div className="flex items-center justify-between text-lg mt-2">
          <span className="font-extrabold">Total</span>
          <span className="font-extrabold text-orange-600">{inr(cartTotal + deliveryFee)}</span>
        </div>
        <button
          disabled={cart.length === 0}
          onClick={() => setView("checkout")}
          className="w-full mt-3 py-3 rounded-2xl bg-orange-600 text-white font-bold disabled:opacity-50"
        >
          Proceed to Pay
        </button>
        <div className="text-[11px] text-neutral-500 mt-1">Always 30–40% cheaper than Zomato.</div>
      </div>
    </div>
  </div>

  <BottomBar cartCount={cart.reduce((s, l) => s + l.qty, 0)} onCart={() => {}} onOffers={() => setView("offers")} onProfile={() => setView("profile")} active="cart" />
</div>

);

const Checkout = ( <div className="max-w-md mx-auto pb-24"> <TopBar title="Checkout" onBack={() => setView("cart")} /> <div className="p-3 space-y-3"> <section className="rounded-2xl border p-3 bg-white"> <div className="flex items-center gap-2 text-sm font-semibold"><MapPin size={16} className="text-orange-600" />Delivery Address</div> <input value={address.label} onChange={(e) => setAddress({ ...address, label: e.target.value })} className="mt-2 w-full border rounded-xl p-2 text-sm" /> <div className="text-[11px] text-neutral-500 mt-1">Free delivery within 3 km.</div> </section>

<section className="rounded-2xl border p-3 bg-white">
      <div className="flex items-center gap-2 text-sm font-semibold"><Wallet size={16} className="text-orange-600" />Payment</div>
      <div className="mt-2 grid grid-cols-2 gap-2 text-sm">
        <button className="p-3 rounded-xl border bg-neutral-50 font-semibold">UPI</button>
        <button className="p-3 rounded-xl border">Cash on Delivery</button>
      </div>
    </section>

    <section className="rounded-2xl border p-3 bg-white">
      <div className="flex items-center justify-between text-sm">
        <span>Items Total</span>
        <span className="font-semibold">{inr(cartTotal)}</span>
      </div>
      <div className="flex items-center justify-between text-sm mt-1">
        <span>Delivery Fee</span>
        <span className="font-semibold">{deliveryFee === 0 ? "FREE" : inr(deliveryFee)}</span>
      </div>
      <div className="flex items-center justify-between text-lg mt-2">
        <span className="font-extrabold">To Pay</span>
        <span className="font-extrabold text-orange-600">{inr(cartTotal + deliveryFee)}</span>
      </div>
    </section>

    <button
      onClick={() => {
        const id = Math.random().toString(36).slice(2, 9);
        setOrderPlaced({ id, status: "placed", total: cartTotal + deliveryFee, items: cart });
        clearCart();
        setView("tracking");
      }}
      className="w-full py-3 rounded-2xl bg-orange-600 text-white font-bold"
    >
      Place Order
    </button>
  </div>
  <BottomBar cartCount={0} onCart={() => setView("cart")} onOffers={() => setView("offers")} onProfile={() => setView("profile")} />
</div>

);

const Tracking = ( <div className="max-w-md mx-auto pb-24"> <TopBar title="Order Tracking" onBack={() => setView("home")} /> {!orderPlaced ? ( <div className="p-6 text-center">No recent order.</div> ) : ( <div className="p-3 space-y-3"> <div className="rounded-3xl border p-4 bg-white"> <div className="flex items-center gap-3"> <div className="h-12 w-12 rounded-2xl bg-orange-100 grid place-items-center text-orange-700"> <Bike /> </div> <div> <div className="font-bold">Rider – Aman (4.8★)</div> <div className="text-xs text-neutral-600">{orderPlaced.status === "preparing" && "Preparing your food…"} {orderPlaced.status === "out" && "Out for delivery"} {orderPlaced.status === "delivered" && "Delivered"} </div> </div> <button className="ml-auto p-2 rounded-xl border"><Phone size={18} /></button> </div> <div className="mt-4"> <Progress status={orderPlaced.status} /> </div> </div>

<div className="rounded-3xl border p-4 bg-white">
        <div className="font-semibold mb-2">Order Summary</div>
        <div className="flex items-center justify-between text-sm"><span>Order ID</span><span className="font-mono">#{orderPlaced.id}</span></div>
        <div className="flex items-center justify-between text-sm"><span>Amount Paid</span><span className="font-semibold">{inr(orderPlaced.total)}</span></div>
      </div>
    </div>
  )}
  <BottomBar cartCount={0} onCart={() => setView("cart")} onOffers={() => setView("offers")} onProfile={() => setView("profile")} />
</div>

);

const Offers = ( <div className="max-w-md mx-auto pb-24"> <TopBar title="Offers & Subscription" onBack={() => setView("home")} /> <div className="p-3 grid gap-3"> <OfferCard title="Student Pass" subtitle="₹999 / month = 30 meals" bullets={["Valid for momos/burger/rolls", "1 meal per day", "Free delivery"]} /> <OfferCard title="Refer & Earn" subtitle="Refer 5 friends = 1 Burger Free" bullets={["Both get ₹30 wallet credit", "No minimum order"]} /> <OfferCard title="Combo Mania" subtitle="Flat ₹20 off on 2+ items" bullets={["Auto-applies in cart", "Weekdays only"]} /> </div> <BottomBar cartCount={cart.reduce((s, l) => s + l.qty, 0)} onCart={() => setView("cart")} onOffers={() => {}} onProfile={() => setView("profile")} active="offers" /> </div> );

const Profile = ( <div className="max-w-md mx-auto pb-24"> <TopBar title="Profile" onBack={() => setView("home")} /> <div className="p-3 space-y-3"> <div className="rounded-3xl border p-4 bg-white flex items-center gap-3"> <div className="h-12 w-12 rounded-2xl bg-orange-100 grid place-items-center text-orange-700 font-extrabold">FR</div> <div> <div className="font-bold">FastBite User</div> <div className="text-xs text-neutral-600">rahul@example.com</div> </div> </div> <div className="rounded-3xl border p-4 bg-white"> <div className="font-semibold">Saved Address</div> <div className="text-sm mt-1">{address.label}</div> </div> <div className="rounded-3xl border p-4 bg-white"> <div className="font-semibold">Support</div> <div className="text-sm mt-1">WhatsApp: +91-90000-00000</div> </div> </div> <BottomBar cartCount={cart.reduce((s, l) => s + l.qty, 0)} onCart={() => setView("cart")} onOffers={() => setView("offers")} onProfile={() => {}} active="profile" /> </div> );

return ( <div className="min-h-screen bg-neutral-50 text-neutral-900"> {view === "home" && Home} {view === "cart" && CartView} {view === "checkout" && Checkout} {view === "tracking" && Tracking} {view === "offers" && Offers} {view === "profile" && Profile} </div> );

// --- Local subcomponents in scope ---------------------------------------- function OfferCard({ title, subtitle, bullets }) { return ( <div className="rounded-3xl border p-4 bg-white shadow-sm"> <div className="flex items-center gap-2"> <Gift className="text-orange-600" /> <div> <div className="font-extrabold">{title}</div> <div className="text-sm text-neutral-600">{subtitle}</div> </div> </div> <ul className="list-disc pl-5 mt-2 text-sm text-neutral-700"> {bullets.map((b, i) => (<li key={i}>{b}</li>))} </ul> </div> ); }

function Progress({ status }) { const steps = [ { id: "placed", label: "Order Placed" }, { id: "preparing", label: "Preparing" }, { id: "out", label: "Out for Delivery" }, { id: "delivered", label: "Delivered" }, ]; const idx = steps.findIndex(s => s.id === status); return ( <div className="flex items-center justify-between"> {steps.map((s, i) => ( <div key={s.id} className="flex-1 flex items-center"> <div className={classNames("h-9 w-9 rounded-full grid place-items-center border", i <= idx ? "bg-orange-600 text-white border-orange-600" : "bg-white text-neutral-500")}> {i < idx ? <CheckCircle2 size={18} /> : i === idx ? <Clock size={18} /> : <Clock size={18} />} </div> {i < steps.length - 1 && ( <div className={classNames("h-1 flex-1 mx-1 rounded-full", i < idx ? "bg-orange-600" : "bg-neutral-200")} /> )} <div className="sr-only">{s.label}</div> </div> ))} </div> ); } }

// --- Add-on selector using ref to collect selection from sheet ------------- const selectedAddonsRef = { current: [] }; function AddonSelector({ item }) { const [selected, setSelected] = useState([]); useEffect(() => { selectedAddonsRef.current = selected; }, [selected]); if (!item.addons?.length) return null; return ( <div className="grid grid-cols-2 gap-2"> {item.addons.map((a) => { const active = selected.includes(a.id); return ( <button key={a.id} onClick={() => setSelected(prev => active ? prev.filter(id => id !== a.id) : [...prev, a.id])} className={classNames("p-2 rounded-xl border text-sm text-left", active ? "bg-orange-50 border-orange-300" : "bg-white")} > <div className="font-semibold">{a.name}</div> <div className="text-xs text-neutral-600">+{inr(a.price)}</div> </button> ); })} </div> ); }
