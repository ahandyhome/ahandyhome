<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hometowne Handyman - Service Portal</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background-color: #f1f5f9; color: #0f172a; }
        
        .custom-scrollbar::-webkit-scrollbar { width: 5px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: transparent; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }

        @media print {
            .no-print { display: none !important; }
            body { background: white; padding: 0; }
            .invoice-container { box-shadow: none !important; border: none !important; margin: 0 !important; width: 100% !important; max-width: 100% !important; }
        }

        .accordion-content { transition: max-height 0.3s ease-out; overflow: hidden; max-height: 0; }
        .accordion-content.open { max-height: 5000px; }
        
        .loading-shimmer {
            background: linear-gradient(90deg, #f1f5f9 25%, #e2e8f0 50%, #f1f5f9 75%);
            background-size: 200% 100%;
            animation: shimmer 1.5s infinite;
        }
        @keyframes shimmer { 0% { background-position: 200% 0; } 100% { background-position: -200% 0; } }

        .toast {
            position: fixed; bottom: 2rem; right: 2rem; background: #0f172a; color: white;
            padding: 1rem 2rem; border-radius: 1rem; box-shadow: 0 10px 25px rgba(0,0,0,0.2);
            z-index: 1000; display: none; transform: translateY(100px); transition: 0.3s;
        }
        .toast.show { display: block; transform: translateY(0); }
    </style>
</head>
<body class="p-4 lg:p-10">

    <div id="toast" class="toast no-print font-bold text-sm"></div>

    <div class="max-w-[1600px] mx-auto grid grid-cols-1 lg:grid-cols-12 gap-10">
        
        <!-- SIDEBAR: SERVICE DIRECTORY -->
        <div id="controlsSidebar" class="lg:col-span-5 no-print space-y-6 h-screen overflow-y-auto pr-4 custom-scrollbar sticky top-10">
            
            <!-- COMPANY INFO HEADER (SIDEBAR) -->
            <div class="px-2 mb-2">
                <h1 class="text-xl font-black text-slate-900 leading-tight">Your Hometowne Handyman LLC</h1>
                <p class="text-xs font-bold text-blue-600">(821) 888-7726</p>
                <p class="text-[10px] font-medium text-slate-400">schandyman4u@gmail.com</p>
            </div>

            <div class="bg-white p-8 rounded-[32px] shadow-xl border border-slate-200">
                <div class="flex items-center gap-4 mb-8">
                    <div class="bg-blue-600 p-3 rounded-2xl text-white shadow-lg shadow-blue-200">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10" /></svg>
                    </div>
                    <div>
                        <h2 class="text-2xl font-extrabold tracking-tight" id="catalogTitle">Service Catalog</h2>
                        <p class="text-xs font-bold text-slate-400 uppercase tracking-widest">Greenville STR Standards</p>
                    </div>
                </div>

                <div class="grid grid-cols-1 gap-4 mb-8">
                    <div>
                        <label class="text-[10px] font-black text-slate-400 uppercase mb-1 block">Client / Property Name</label>
                        <input type="text" id="clientName" oninput="updateInvoice()" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none" placeholder="Enter property name">
                    </div>
                    <div>
                        <label class="text-[10px] font-black text-slate-400 uppercase mb-1 block">Account Membership Tier</label>
                        <select id="tierSelect" onchange="updateInvoice()" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-bold text-blue-600">
                            <option value="standard">Standard Market (+$125 Trip)</option>
                            <option value="bronze">Bronze (-5% + $125 Trip)</option>
                            <option value="silver">Silver (-10% + $125 Trip)</option>
                            <option value="gold">Gold (-15% + $125 Trip)</option>
                            <option value="diamond">Diamond (-20% + Waived Trip)</option>
                            <option value="platinum">Platinum (-25% + Waived Trip)</option>
                        </select>
                    </div>
                </div>

                <!-- Admin Tools Section (Hidden for Clients) -->
                <div id="adminTools" class="flex gap-2 mb-8">
                    <button onclick="generateAI('scope')" class="flex-1 py-3 bg-blue-50 text-blue-700 rounded-xl text-xs font-black uppercase hover:bg-blue-600 hover:text-white transition">AI Scope</button>
                    <button onclick="generateAI('materials')" class="flex-1 py-3 bg-indigo-50 text-indigo-700 rounded-xl text-xs font-black uppercase hover:bg-indigo-600 hover:text-white transition">AI Materials</button>
                </div>

                <div class="relative mb-6">
                    <input type="text" id="searchBox" oninput="filterServices()" class="w-full p-4 pl-12 bg-slate-100 rounded-2xl border-none focus:ring-2 focus:ring-blue-500" placeholder="Search 250+ services...">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 absolute left-4 top-4 text-slate-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" /></svg>
                </div>

                <div id="categoryContainer" class="space-y-3"></div>
            </div>

            <div class="space-y-3 pb-10">
                <button onclick="saveInvoiceToCloud()" id="mainActionBtn" class="w-full py-5 bg-blue-600 text-white rounded-3xl font-black text-lg hover:bg-blue-700 transition-all shadow-xl flex items-center justify-center gap-4">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7H5a2 2 0 00-2 2v9a2 2 0 002 2h14a2 2 0 002-2V9a2 2 0 00-2-2h-3m-1 4l-3 3m0 0l-3-3m3 3V4" /></svg>
                    <span id="btnText">SAVE & GENERATE LINK</span>
                </button>
                <button onclick="window.print()" class="w-full py-5 bg-slate-900 text-white rounded-3xl font-black text-lg hover:bg-black transition-all shadow-xl flex items-center justify-center gap-4">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 17h2a2 2 0 002-2v-4a2 2 0 00-2-2H5a2 2 0 00-2 2v4a2 2 0 002 2h2m2 4h6a2 2 0 002-2v-4a2 2 0 00-2-2H9a2 2 0 00-2 2v4a2 2 0 002 2zm8-12V5a2 2 0 00-2-2H9a2 2 0 00-2 2v4h10z" /></svg>
                    PRINT TO PDF
                </button>
            </div>
        </div>

        <!-- MAIN INVOICE PREVIEW -->
        <div class="lg:col-span-7" id="invoiceArea">
            <div id="invoice" class="invoice-container bg-white p-16 rounded-[48px] shadow-2xl border border-slate-100 min-h-[1200px] flex flex-col relative">
                
                <div class="flex justify-between items-start mb-20">
                    <div>
                        <h1 id="docTitle" class="text-5xl font-black text-slate-900 tracking-tighter mb-4">INVOICE</h1>
                        <div class="space-y-1">
                            <p class="text-xl font-extrabold text-blue-600">Your Hometowne Handyman LLC</p>
                            <p class="text-slate-500 font-bold text-sm">(821) 888-7726</p>
                            <p class="text-slate-500 font-bold text-sm">schandyman4u@gmail.com</p>
                        </div>
                    </div>
                    <div class="text-right">
                        <div class="bg-slate-900 text-white px-6 py-3 rounded-2xl inline-block">
                            <p class="text-[10px] font-black uppercase tracking-widest opacity-50">Date</p>
                            <p id="invDate" class="font-bold"></p>
                        </div>
                        <div id="invoiceIdDisplay" class="mt-4 text-[10px] font-black text-slate-300 uppercase tracking-widest"></div>
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-12 border-y border-slate-100 py-12 mb-12">
                    <div>
                        <p class="text-[10px] font-black text-slate-400 uppercase tracking-widest mb-2">Location</p>
                        <h2 id="dispClient" class="text-3xl font-black text-slate-900 italic">Select Property</h2>
                    </div>
                    <div class="text-right">
                        <p class="text-[10px] font-black text-slate-400 uppercase tracking-widest mb-2">Account Status</p>
                        <span id="dispTier" class="px-4 py-1.5 bg-blue-50 text-blue-600 text-xs font-black rounded-full border border-blue-100 inline-block uppercase"></span>
                    </div>
                </div>

                <div id="aiBlock" class="hidden mb-12 p-8 bg-slate-50 rounded-[32px] border border-slate-100 relative group">
                    <button onclick="this.parentElement.remove()" class="no-print absolute top-4 right-4 text-slate-300 hover:text-slate-600">×</button>
                    <p class="text-[10px] font-black text-blue-600 uppercase mb-3 flex items-center gap-2">✨ Project Scope Details</p>
                    <div id="aiText" class="text-sm font-medium text-slate-700 italic leading-relaxed"></div>
                </div>

                <div class="flex-grow">
                    <table class="w-full">
                        <thead>
                            <tr class="border-b-2 border-slate-900">
                                <th class="text-left py-6 text-[10px] font-black text-slate-400 uppercase tracking-widest">Service Details</th>
                                <th class="text-right py-6 text-[10px] font-black text-slate-400 uppercase tracking-widest w-24">Market</th>
                                <th class="text-right py-6 text-[10px] font-black text-slate-400 uppercase tracking-widest w-24">Member</th>
                            </tr>
                        </thead>
                        <tbody id="lineItems"></tbody>
                    </table>
                </div>

                <div class="mt-20 pt-12 border-t-2 border-slate-100 flex justify-end">
                    <div class="w-80 space-y-4">
                        <div class="flex justify-between text-slate-400 font-bold text-xs uppercase">
                            <span>Total Market Value</span>
                            <span id="mTotal">$0.00</span>
                        </div>
                        <div class="flex justify-between text-green-600 font-black text-xs uppercase bg-green-50 px-4 py-3 rounded-xl">
                            <span>Member Savings</span>
                            <span id="sTotal">-$0.00</span>
                        </div>
                        <div class="flex justify-between items-end pt-6 border-t border-slate-100">
                            <span class="text-xl font-black">ESTIMATED TOTAL</span>
                            <span id="fTotal" class="text-4xl font-black text-blue-600 tracking-tighter">$0.00</span>
                        </div>
                    </div>
                </div>

                <div class="mt-24 pt-10 opacity-60 text-center border-t border-slate-100">
                    <p class="text-[10px] font-bold text-slate-400 uppercase tracking-[0.2em] mb-4">Quality Service • Upstate Professionalism • STR Specialists</p>
                </div>
            </div>
        </div>
    </div>

    <!-- Firebase SDKs -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, setDoc, getDoc, collection } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // CONFIG
        const firebaseConfig = JSON.parse(__firebase_config);
        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'hometowne-handyman';
        const apiKey = "";

        // STATE
        let currentUser = null;
        let selectedItems = [];
        let currentInvoiceId = new URLSearchParams(window.location.search).get('id');
        let viewMode = new URLSearchParams(window.location.search).get('view') || 'admin';

        // COMPREHENSIVE SERVICE LIST - 50+ per section
        const services = {
            "Electrical (STR & Residential)": [
                { name: "Smart Lock Installation (August/Yale/Schlage)", price: 145 },
                { name: "GFCI Outlet Replacement (Kitchen/Bath)", price: 115 },
                { name: "Ceiling Fan Installation (Standard)", price: 215 },
                { name: "Ceiling Fan w/ Remote Programming", price: 245 },
                { name: "Smoke/CO2 Detector (Hardwired)", price: 95 },
                { name: "Smoke Detector Battery Service (Whole House)", price: 125 },
                { name: "Ring/Nest Doorbell Hardwire", price: 165 },
                { name: "Light Switch Dimmer Upgrade", price: 85 },
                { name: "USB Wall Outlet Conversion", price: 95 },
                { name: "Exterior Security Light Installation", price: 185 },
                { name: "Recessed Lighting Trim Upgrade", price: 75 },
                { name: "Smart Thermostat Setup (Ecobee/Nest)", price: 155 },
                { name: "Kitchen Undercabinet Lighting (LED)", price: 285 },
                { name: "Chandelier Replacement (up to 12ft)", price: 325 },
                { name: "Troubleshoot Dead Circuit/Outlet", price: 145 },
                { name: "Wall Mounted TV Power Relocation Kit", price: 225 },
                { name: "Bathroom Exhaust Fan Replacement", price: 195 },
                { name: "Exhaust Fan Motor Cleaning & Lube", price: 115 },
                { name: "Standard Receptacle Replacement (White/Ivory)", price: 65 },
                { name: "Three-Way Switch Repair/Replacement", price: 95 },
                { name: "Weatherproof Exterior Outlet Cover Install", price: 85 },
                { name: "Floor Outlet Installation", price: 245 },
                { name: "Pendant Light Installation", price: 165 },
                { name: "Fluorescent to LED Conversion (Ballast Bypass)", price: 145 },
                { name: "Landscape Lighting Transformer Repair", price: 175 },
                { name: "Solar Outdoor Path Light Assembly (Set of 6)", price: 95 },
                { name: "Motion Sensor Light Tuning", price: 75 },
                { name: "Doorbell Transformer Replacement", price: 135 },
                { name: "Attic Light Pull-Chain Repair", price: 85 },
                { name: "Closet Lighting Automatic Switch Install", price: 145 },
                { name: "Garage Door Opener Sensor Realignment", price: 95 },
                { name: "Garage Door Keypad Programming", price: 75 },
                { name: "Hardwired Under-Counter Power Strip", price: 185 },
                { name: "Holiday Light Hanging (Standard Gutter Run)", price: 250 },
                { name: "Pool Pump Timer Replacement", price: 225 },
                { name: "Post Light Bulb & Sensor Service", price: 95 },
                { name: "Ceiling Light Fixture Glass Replacement", price: 75 },
                { name: "Smart Plug/Bridge Configuration (Per 3 devices)", price: 115 },
                { name: "Home Audio Speaker Wiring (Exposed)", price: 165 },
                { name: "Wall Mounted Tablet Dock Installation", price: 195 },
                { name: "Track Lighting Head Replacement", price: 85 },
                { name: "Electric Baseboard Heater Troubleshooting", price: 145 },
                { name: "Surge Protection Power Strip Cleanup", price: 75 },
                { name: "Internet Router/Modem Cable Management", price: 125 },
                { name: "Doorbell Chime Box Replacement", price: 115 },
                { name: "Battery Backup (UPS) Installation", price: 95 },
                { name: "Emergency Exit Light Battery Swap", price: 115 },
                { name: "Wine Cooler Electrical Troubleshooting", price: 165 },
                { name: "Main Panel Labeling/Identification", price: 185 },
                { name: "Ground Wire Inspection & Tightening", price: 125 },
                { name: "EV Charger Cable Management Bracket Install", price: 85 }
            ],
            "Plumbing (Kitchen & Bath)": [
                { name: "Toilet Internal Rebuild (Fill/Flush Valve)", price: 155 },
                { name: "Kitchen Faucet Replacement", price: 195 },
                { name: "Bathroom Sink Faucet Install", price: 165 },
                { name: "Garbage Disposal Install (1/2 HP)", price: 215 },
                { name: "Garbage Disposal Install (3/4 HP Pro)", price: 265 },
                { name: "Shower Head Upgrade", price: 110 },
                { name: "Drain Snaking (Sink/Tub)", price: 135 },
                { name: "Supply Line Leak Repair", price: 95 },
                { name: "Toilet Wax Ring/Re-seat", price: 185 },
                { name: "Toilet Replacement (New Unit)", price: 295 },
                { name: "Refrigerator Water Line Install", price: 165 },
                { name: "Caulk Bathtub/Shower Perimeter", price: 145 },
                { name: "Dishwasher Installation", price: 225 },
                { name: "P-Trap Replacement", price: 115 },
                { name: "Bidet Attachment Installation", price: 125 },
                { name: "Shower Door Roller Repair", price: 145 },
                { name: "Bathtub Overflow Plate Replacement", price: 85 },
                { name: "Pop-Up Drain Assembly (Bathroom)", price: 115 },
                { name: "Aerator Cleaning/Replacement (Whole House)", price: 95 },
                { name: "Angle Stop Valve Replacement", price: 125 },
                { name: "Exterior Hose Bib Repair (Washer Replacement)", price: 115 },
                { name: "Exterior Hose Bib Replacement (New Unit)", price: 195 },
                { name: "Irrigation Head Replacement (Single)", price: 65 },
                { name: "Sump Pump Inspection & Testing", price: 115 },
                { name: "Washing Machine Hose Upgrade (Stainless)", price: 115 },
                { name: "Toilet Handle Replacement", price: 65 },
                { name: "Toilet Flapper Swap", price: 55 },
                { name: "Shower Valve Handle Cartridge Replacement", price: 165 },
                { name: "Shower Arm Replacement", price: 95 },
                { name: "Bathroom Vanity Installation (Cabinet Only)", price: 245 },
                { name: "Vanity Sink Basin Caulking", price: 95 },
                { name: "Kitchen Sink Basket Strainer Replacement", price: 135 },
                { name: "Under-Sink Water Filtration System (Single Stage)", price: 185 },
                { name: "Reverse Osmosis Filter Maintenance", price: 145 },
                { name: "Icemaker Filter Replacement", price: 85 },
                { name: "Sewer Cleanout Cap Replacement", price: 65 },
                { name: "Septic Tank Riser Lid Bolt Replacement", price: 95 },
                { name: "Whirlpool Tub Jet Cleaning Service", price: 165 },
                { name: "Shower Caddy Tension Pole Install", price: 85 },
                { name: "Towel Bar/Ring Installation", price: 75 },
                { name: "Toilet Paper Holder Install", price: 65 },
                { name: "Grab Bar Safety Install (Stud Mount)", price: 145 },
                { name: "Dishwasher Air Gap Replacement", price: 85 },
                { name: "Floor Drain Cleaning/Flushing", price: 115 },
                { name: "Expansion Tank Inspection", price: 95 },
                { name: "Water Heater Flushing Service", price: 145 },
                { name: "Pressure Reducing Valve (PRV) Test", price: 85 },
                { name: "Cabinet Water Guard Tray Install", price: 75 },
                { name: "Condensate Drain Line Cleaning", price: 135 },
                { name: "Kitchen Pull-Out Spray Hose Replacement", price: 115 },
                { name: "Urinal Flange Repair (Commercial Lite)", price: 225 }
            ],
            "Interior Repairs & Cosmetics": [
                { name: "Drywall Patch & Texture (Minor)", price: 165 },
                { name: "Drywall Repair (Large Hole 1ft+)", price: 245 },
                { name: "Baseboard/Trim Repair (10ft)", price: 145 },
                { name: "Interior Door Shave/Alignment", price: 115 },
                { name: "Cabinet Hinge Tuning (Whole Kitchen)", price: 185 },
                { name: "Cabinet Knob/Handle Install (Set of 10)", price: 125 },
                { name: "LVP Plank Replacement", price: 185 },
                { name: "Furniture Assembly (Large)", price: 250 },
                { name: "Furniture Assembly (Medium/Dresser)", price: 175 },
                { name: "Blind/Shade Installation", price: 95 },
                { name: "Mirror/Art Hanging (Heavy)", price: 125 },
                { name: "TV Wall Mount (up to 65 inch)", price: 185 },
                { name: "Popcorn Ceiling Spot Repair", price: 195 },
                { name: "Handrail Tightening", price: 110 },
                { name: "Door Knob/Deadbolt Swap", price: 85 },
                { name: "Sliding Barn Door Installation", price: 325 },
                { name: "Pocket Door Roller Repair", price: 195 },
                { name: "Closet Rod/Shelf Install", price: 145 },
                { name: "Bi-Fold Door Adjustment/Track Lube", price: 95 },
                { name: "Door Stopper Installation (Floor/Wall)", price: 45 },
                { name: "Interior Window Sill Repair", price: 165 },
                { name: "Crown Molding Corner Gap Repair", price: 125 },
                { name: "Shoe Molding Installation (Per Room)", price: 185 },
                { name: "Stair Baluster Re-gluing", price: 95 },
                { name: "Floor Transition Strip Replacement", price: 115 },
                { name: "Grout Cleaning & Sealing (Small Bath)", price: 225 },
                { name: "Loose Tile Re-adhesion (Floor)", price: 145 },
                { name: "Backsplash Caulking (Kitchen)", price: 115 },
                { name: "Picture Rail Molding Install", price: 195 },
                { name: "Acoustic Panel Wall Mounting", price: 165 },
                { name: "Childproofing (Outlet Covers/Latches)", price: 125 },
                { name: "Window Screen Repair (Single)", price: 75 },
                { name: "Window Crank Handle Replacement", price: 85 },
                { name: "Attic Ladder Hinge Lube & Tension", price: 115 },
                { name: "Draft Dodger/Weatherstripping Interior", price: 95 },
                { name: "Peel & Stick Wallpaper (Accent Wall)", price: 275 },
                { name: "Vinyl Floor Scratch Repair", price: 135 },
                { name: "Furniture Touch-up (Marker/Wax)", price: 85 },
                { name: "Mirror Frame Installation (Glue-on)", price: 145 },
                { name: "Ceiling Medalion Installation", price: 125 },
                { name: "Closet Organizer System Build", price: 350 },
                { name: "Laundry Room Shelf Installation", price: 165 },
                { name: "Mantle Installation (Wall Mount)", price: 225 },
                { name: "Shadow Box/Wainscoting Repair", price: 185 },
                { name: "Floor Joist Squeak Repair (Top Side)", price: 195 },
                { name: "Wall Safe Installation", price: 245 },
                { name: "Door Weatherstrip (Bottom Sweep)", price: 75 },
                { name: "Cabinet Drawer Slide Replacement", price: 115 },
                { name: "Countertop Chip Repair (Granite/Quartz)", price: 195 },
                { name: "Smoke Stained Wall Sealing (Small Area)", price: 165 },
                { name: "Wallpaper Removal (Per Hour)", price: 95 }
            ],
            "Exterior & Curb Appeal": [
                { name: "Gutter Cleaning (1-Story)", price: 175 },
                { name: "Gutter Cleaning (2-Story)", price: 275 },
                { name: "Pressure Wash Walkways/Entry", price: 215 },
                { name: "Fence Gate Sag Repair", price: 135 },
                { name: "House Number Install", price: 75 },
                { name: "Exterior Trim Touch-up", price: 195 },
                { name: "Mailbox/Post Replacement", price: 225 },
                { name: "Deck Board Swap (Up to 3)", price: 165 },
                { name: "Window Screen Repair", price: 85 },
                { name: "Outdoor Furniture Build", price: 145 },
                { name: "Driveway Crack Sealing", price: 185 },
                { name: "Siding Spot Repair (Vinyl)", price: 195 },
                { name: "Porch Screen Repair", price: 155 },
                { name: "Exterior Door Weatherstripping", price: 115 },
                { name: "Shutters Installation/Repair", price: 145 },
                { name: "Flagpole Mount Installation", price: 95 },
                { name: "Solar Light Installation (Post Mount)", price: 115 },
                { name: "Flower Box Window Mount", price: 135 },
                { name: "Exterior Caulk Refresh (Per Window)", price: 65 },
                { name: "Deck Railing Tightening", price: 145 },
                { name: "Fence Post Reinforcement (Brackets)", price: 185 },
                { name: "Soffit Vent Cleaning/Unclogging", price: 165 },
                { name: "Exterior Faucet Insulation Cover", price: 45 },
                { name: "Stucco Patch (Minor)", price: 195 },
                { name: "Wood Rot Patch (Small/Epoxy)", price: 175 },
                { name: "Foundation Vent Screen Install", price: 125 },
                { name: "Retaining Wall Cap Stone Reset", price: 145 },
                { name: "Bird Nest/Nuisance Removal", price: 165 },
                { name: "Dryer Vent Hood Replacement", price: 115 },
                { name: "House Name Sign Custom Mount", price: 95 },
                { name: "Paver Sand Refill (Small Area)", price: 185 },
                { name: "Outdoor Kitchen Hinge Lube", price: 95 },
                { name: "Gutter Downspout Extension Install", price: 85 },
                { name: "Rain Barrel Setup", price: 145 },
                { name: "Shed Door Hinge Repair", price: 135 },
                { name: "Outdoor Storage Box Assembly", price: 125 },
                { name: "Grill Cleaning (Premium Deep Clean)", price: 195 },
                { name: "Pool Area Pressure Washing", price: 275 },
                { name: "Basketball Hoop Assembly", price: 350 },
                { name: "Trampoline Assembly (Standard)", price: 295 },
                { name: "Swing Set Inspection & Bolt Tightening", price: 165 },
                { name: "Fence Picket Replacement (Up to 5)", price: 145 },
                { name: "Stone Walkway Leveling (Minor)", price: 225 },
                { name: "Exterior Light Bulb Replacement (High)", price: 115 },
                { name: "Hose Reel Wall Mounting", price: 95 },
                { name: "Christmas Light Removal (Standard)", price: 185 },
                { name: "Privacy Screen Installation", price: 245 },
                { name: "Deck Stair Tread Replacement", price: 165 },
                { name: "Outdoor Fan Balancing", price: 115 },
                { name: "Siding Pressure Washing (Partial Wall)", price: 195 },
                { name: "Eave/Fascia Painting (Touch up)", price: 225 }
            ],
            "Appliances & Maintenance": [
                { name: "Dryer Vent Cleaning (Standard)", price: 165 },
                { name: "AC Filter Change (Whole House)", price: 85 },
                { name: "Range Hood Installation", price: 195 },
                { name: "Microwave Over-Range Install", price: 215 },
                { name: "Washer/Dryer Hookup", price: 125 },
                { name: "Refrigerator Coil Cleaning", price: 95 },
                { name: "Fridge Gasket Cleaning & Lube", price: 85 },
                { name: "Stove Anti-Tip Bracket Install", price: 115 },
                { name: "Dishwasher Unclog (Filter Area)", price: 135 },
                { name: "Washing Machine Leveling", price: 95 },
                { name: "Dryer Internal Lint Trap Vac", price: 145 },
                { name: "Fridge Water Filter Swap", price: 65 },
                { name: "Icemaker Arm Realignment", price: 85 },
                { name: "Oven Door Gasket Swap", price: 125 },
                { name: "Deep Freezer Defrosting Assistance", price: 145 },
                { name: "Wine Fridge Temperature Check", price: 75 },
                { name: "Vent Hood Filter Degreasing", price: 95 },
                { name: "Cabinet Door Soft Close Buffer Install", price: 75 },
                { name: "Gas Stove Burner Cleaning", price: 115 },
                { name: "Trash Compactor Bag/Deodorizer Service", price: 85 },
                { name: "Coffee Machine Descaling (Built-in)", price: 125 },
                { name: "Stand Mixer Gear Regreasing", price: 165 },
                { name: "Vac System Hose Clog Removal", price: 115 },
                { name: "Air Purifier Filter Swap", price: 65 },
                { name: "Dehumidifier Tank Cleaning", price: 85 },
                { name: "Humidifier Pad Replacement", price: 95 },
                { name: "Space Heater Safety Check", price: 75 },
                { name: "Propane Tank Exchange Delivery", price: 95 },
                { name: "Pellet Stove Cleaning (Basic)", price: 195 },
                { name: "Fireplace Screen Repair", price: 115 },
                { name: "Chimney Cap Inspection (Visual)", price: 95 },
                { name: "Whole House Fan Maintenance", price: 165 },
                { name: "Attic Fan Solar Check", price: 145 },
                { name: "Wall AC Unit Seasonal Install", price: 135 },
                { name: "Portable AC Window Seal Setup", price: 115 },
                { name: "Generator Battery Testing", price: 95 },
                { name: "Battery Storage Maintenance (UPS)", price: 115 },
                { name: "Home Gym Treadmill Belt Lube", price: 145 },
                { name: "Stationary Bike Leveling", price: 95 },
                { name: "Massage Chair Setup/Unboxing", price: 185 },
                { name: "Security Camera Battery Swap", price: 85 },
                { name: "Smart Hub Network Reconnect", price: 115 },
                { name: "WiFi Dead Zone Booster Setup", price: 135 },
                { name: "Digital Lock PIN Reset/Admin", price: 95 },
                { name: "Appliance Bulb Replacement", price: 65 },
                { name: "Kitchen Kickplate Re-attachment", price: 85 },
                { name: "Adjustable Bed Frame Troubleshooting", price: 165 },
                { name: "Lift Chair Mechanism Lube", price: 145 },
                { name: "Wine Rack Assembly/Mounting", price: 125 },
                { name: "Safe Battery Replacement", price: 75 },
                { name: "Pantry Inventory/Organization", price: 185 }
            ]
        };

        const discountMap = { standard: 0, bronze: 0.05, silver: 0.1, gold: 0.15, diamond: 0.20, platinum: 0.25 };

        onAuthStateChanged(auth, async (user) => {
            if (!user) {
                await signInAnonymously(auth);
            } else {
                currentUser = user;
                setupUIForMode();
                if (currentInvoiceId) {
                    loadInvoice(currentInvoiceId);
                } else {
                    renderCategories();
                    updateInvoice();
                }
            }
        });

        function setupUIForMode() {
            const btnText = document.getElementById('btnText');
            const docTitle = document.getElementById('docTitle');
            const adminTools = document.getElementById('adminTools');

            if (viewMode === 'client') {
                btnText.innerText = "SUBMIT SERVICE REQUEST";
                docTitle.innerText = "SERVICE QUOTE";
                adminTools.classList.add('hidden');
            } else {
                btnText.innerText = "SAVE & GENERATE LINK";
                docTitle.innerText = "INVOICE";
                adminTools.classList.remove('hidden');
            }
        }

        async function loadInvoice(id) {
            const docRef = doc(db, 'artifacts', appId, 'public', 'data', 'invoices', id);
            const docSnap = await getDoc(docRef);
            if (docSnap.exists()) {
                const data = docSnap.data();
                document.getElementById('clientName').value = data.client;
                document.getElementById('tierSelect').value = data.tier;
                selectedItems = data.items;
                if (data.aiScope) {
                    document.getElementById('aiBlock').classList.remove('hidden');
                    document.getElementById('aiText').innerText = data.aiScope;
                }
                document.getElementById('invoiceIdDisplay').innerText = `Ref: ${id}`;
                updateInvoice();
            }
        }

        window.saveInvoiceToCloud = async () => {
            if (!currentUser) return;
            const isClientMode = viewMode === 'client';
            const id = currentInvoiceId || crypto.randomUUID().substring(0, 8).toUpperCase();
            
            const invoiceData = {
                client: document.getElementById('clientName').value,
                tier: document.getElementById('tierSelect').value,
                items: selectedItems,
                aiScope: document.getElementById('aiText').innerText,
                date: new Date().toISOString(),
                total: document.getElementById('fTotal').innerText,
                submittedBy: isClientMode ? 'client' : 'admin'
            };

            try {
                await setDoc(doc(db, 'artifacts', appId, 'public', 'data', 'invoices', id), invoiceData);
                currentInvoiceId = id;
                if (isClientMode) {
                    showToast("Request Submitted! We will contact you shortly.");
                } else {
                    const url = `${window.location.origin}${window.location.pathname}?id=${id}&view=client`;
                    const dummy = document.createElement("input");
                    document.body.appendChild(dummy);
                    dummy.value = url;
                    dummy.select();
                    document.execCommand("copy");
                    document.body.removeChild(dummy);
                    showToast(`Saved! Client link copied to clipboard.`);
                }
                document.getElementById('invoiceIdDisplay').innerText = `Ref: ${id}`;
            } catch (e) {
                showToast("Error processing request.");
            }
        };

        function showToast(msg) {
            const t = document.getElementById('toast');
            t.innerText = msg;
            t.classList.add('show');
            setTimeout(() => t.classList.remove('show'), 3000);
        }

        window.addItem = (name, price) => {
            selectedItems.push({ name, price });
            updateInvoice();
        };

        window.updateInvoice = () => {
            const tier = document.getElementById('tierSelect').value;
            const discPercent = discountMap[tier];
            const client = document.getElementById('clientName').value || "Select Property";
            const waiveTripFee = (tier === 'diamond' || tier === 'platinum');
            const standardTripFee = 125.00;

            document.getElementById('dispClient').innerText = client;
            document.getElementById('dispTier').innerText = document.getElementById('tierSelect').options[document.getElementById('tierSelect').selectedIndex].text.split('(')[0].trim();
            document.getElementById('invDate').innerText = new Date().toLocaleDateString();

            const tbody = document.getElementById('lineItems');
            tbody.innerHTML = '';
            
            let marketSub = 0;
            let finalSub = 0;

            marketSub += standardTripFee;
            if (waiveTripFee) {
                tbody.appendChild(createRow("Service Visit Trip Fee", standardTripFee, 0));
            } else {
                tbody.appendChild(createRow("Service Visit Trip Fee", standardTripFee, standardTripFee));
                finalSub += standardTripFee;
            }

            selectedItems.forEach((item, idx) => {
                const memberPrice = item.price * (1 - discPercent);
                marketSub += item.price;
                finalSub += memberPrice;
                tbody.appendChild(createRow(item.name, item.price, memberPrice, idx));
            });

            document.getElementById('mTotal').innerText = `$${marketSub.toFixed(2)}`;
            document.getElementById('sTotal').innerText = `-$${(marketSub - finalSub).toFixed(2)}`;
            document.getElementById('fTotal').innerText = `$${finalSub.toFixed(2)}`;
        };

        function createRow(name, market, member, idx = null) {
            const tr = document.createElement('tr');
            tr.className = "border-b border-slate-50 group";
            tr.innerHTML = `
                <td class="py-5">
                    <p class="font-bold text-slate-800 text-sm">${name}</p>
                    ${idx !== null ? `<button onclick="window.removeItem(${idx})" class="no-print text-[9px] text-red-500 font-black uppercase hover:underline opacity-0 group-hover:opacity-100 transition">Remove</button>` : ''}
                </td>
                <td class="py-5 text-right text-xs text-slate-400 font-medium">$${market.toFixed(2)}</td>
                <td class="py-5 text-right font-black text-slate-900 text-sm ${member === 0 ? 'text-green-600' : ''}">${member === 0 ? 'WAIVED' : '$' + member.toFixed(2)}</td>
            `;
            return tr;
        }

        window.removeItem = (idx) => {
            selectedItems.splice(idx, 1);
            updateInvoice();
        };

        window.renderCategories = () => {
            const container = document.getElementById('categoryContainer');
            container.innerHTML = '';
            Object.keys(services).forEach((cat) => {
                const group = document.createElement('div');
                group.className = "bg-slate-50 rounded-2xl border border-slate-200 overflow-hidden";
                group.innerHTML = `
                    <button onclick="window.toggleAccordion(this)" class="w-full p-4 flex justify-between items-center font-bold text-slate-700 hover:bg-slate-100 transition">
                        <span class="flex items-center gap-3">
                            <span class="w-2 h-6 bg-blue-600 rounded-full"></span>
                            ${cat}
                        </span>
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 transition-transform" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M19 9l-7 7-7-7" /></svg>
                    </button>
                    <div class="accordion-content bg-white">
                        <div class="p-2 space-y-1 max-h-[550px] overflow-y-auto custom-scrollbar">
                            ${services[cat].map(s => `
                                <div onclick="window.addItem('${s.name}', ${s.price})" class="flex justify-between items-center p-3 rounded-xl hover:bg-blue-50 cursor-pointer group transition">
                                    <div class="text-xs font-bold text-slate-600 group-hover:text-blue-700">${s.name}</div>
                                    <div class="text-xs font-black text-slate-400">$${s.price}</div>
                                </div>
                            `).join('')}
                        </div>
                    </div>
                `;
                container.appendChild(group);
            });
        };

        window.toggleAccordion = (btn) => {
            const content = btn.nextElementSibling;
            const svg = btn.querySelector('svg');
            content.classList.toggle('open');
            svg.classList.toggle('rotate-180');
        };

        window.filterServices = () => {
            const term = document.getElementById('searchBox').value.toLowerCase();
            const items = document.querySelectorAll('.accordion-content .p-3');
            items.forEach(item => {
                const text = item.innerText.toLowerCase();
                item.style.display = text.includes(term) ? 'flex' : 'none';
                if (term.length > 1) item.closest('.accordion-content').classList.add('open');
            });
        };

        window.generateAI = async (type) => {
            if (selectedItems.length === 0) return;
            const block = document.getElementById('aiBlock');
            const textEl = document.getElementById('aiText');
            block.classList.remove('hidden');
            textEl.innerHTML = `<div class="h-4 w-full loading-shimmer rounded mb-2"></div><div class="h-4 w-2/3 loading-shimmer rounded"></div>`;
            const servicesText = selectedItems.map(i => i.name).join(", ");
            const prompt = type === 'scope' 
                ? `Write a professional 3-sentence summary for a property maintenance report including: ${servicesText}. Use a tone of Greenville, SC expert property care.`
                : `List 5 key technical materials needed for: ${servicesText}.`;

            try {
                const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        contents: [{ parts: [{ text: prompt }] }],
                        systemInstruction: { parts: [{ text: "You are Your Hometowne Handyman LLC. (821) 888-7726. Professional, technical, concise." }] }
                    })
                });
                const data = await response.json();
                textEl.innerText = data.candidates[0].content.parts[0].text;
            } catch (e) {
                textEl.innerText = "Error generating content.";
            }
        };

    </script>
</body>
</html>

