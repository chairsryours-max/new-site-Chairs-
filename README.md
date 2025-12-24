import React, { useState, useEffect, useMemo, useRef } from 'react';
import { 
  Armchair, 
  MapPin, 
  Menu, 
  X, 
  ArrowRight, 
  ChevronDown, 
  Sparkles, 
  Briefcase, 
  Calculator, 
  Layout, 
  Globe,
  Search,
  Package,
  Phone,
  Mail,
  Clock,
  Instagram,
  Facebook,
  Loader2,
  CheckCircle2,
  Calendar,
  Users,
  ShieldCheck,
  Maximize2
} from 'lucide-react';

const App = () => {
  const [isScrolled, setIsScrolled] = useState(false);
  const [mobileMenuOpen, setMobileMenuOpen] = useState(false);
  const [locationMenuOpen, setLocationMenuOpen] = useState(false);
  const [view, setView] = useState('home'); 
  const [currentPage, setCurrentPage] = useState(null); 
  const [activeTab, setActiveTab] = useState('wedding');

  // Planning Tool State
  const [guestCount, setGuestCount] = useState(64);
  const [tableType, setTableType] = useState('round60');

  // Comprehensive Location List
  const locations = [
    "Wake Forest", "Raleigh", "Morrisville", "Apex", "Cary", "Rolesville", 
    "Youngsville", "Franklinton", "Creedmoor", "Louisburg", "Holly Springs"
  ];

  // Dynamic Content Helpers
  const getLocName = () => currentPage || "North Carolina";
  const getSubText = () => currentPage ? `in ${currentPage}, NC` : "across the Raleigh-Durham Area";

  useEffect(() => {
    const handleScroll = () => setIsScrolled(window.scrollY > 50);
    window.addEventListener('scroll', handleScroll);
    
    const script = document.createElement('script');
    script.src = "https://178eb0d5-6142-4d2f-8a08-43abc1808294.assets.booqable.com/v2/booqable.js";
    script.async = true;
    document.body.appendChild(script);

    return () => window.removeEventListener('scroll', handleScroll);
  }, []);

  const scrollToInventory = () => {
    const el = document.getElementById('inventory');
    if (el) el.scrollIntoView({ behavior: 'smooth' });
  };

  const navigateToLocation = (loc) => {
    setCurrentPage(loc);
    setView('home'); 
    setLocationMenuOpen(false);
    setMobileMenuOpen(false);
    window.scrollTo({ top: 0, behavior: 'smooth' });
  };

  const changeView = (v) => {
    setView(v);
    setMobileMenuOpen(false);
    setLocationMenuOpen(false);
    window.scrollTo({ top: 0, behavior: 'smooth' });
  };

  const goHome = () => {
    setCurrentPage(null);
    setView('home');
    setMobileMenuOpen(false);
    window.scrollTo({ top: 0, behavior: 'smooth' });
  };

  const planningData = useMemo(() => {
    const tableCapacities = { 'round60': 8, 'round72': 10, 'rect6': 6, 'rect8': 8 };
    const capacityPerTable = tableCapacities[tableType] || 8;
    const count = Math.ceil(guestCount / capacityPerTable);
    // Limit for visualizer performance but calculate real numbers
    const visualCount = Math.min(count, 48); 
    return { 
      tablesNeeded: count, 
      chairsNeeded: guestCount,
      visualCount,
      isRound: tableType.startsWith('round')
    };
  }, [guestCount, tableType]);

  const InventorySection = ({ title, subtitle }) => (
    <section id="inventory" className="py-24 bg-[#0f172a]">
      <div className="max-w-7xl mx-auto px-6">
        <div className="mb-12 text-center">
          <h2 className="text-3xl md:text-4xl font-black mb-4 uppercase tracking-tighter text-white">
            {title || (currentPage ? `${currentPage} Event Equipment Inventory` : "Live Rental Catalog & Pricing")}
          </h2>
          <p className="text-zinc-400 font-medium">
            {subtitle || (currentPage ? `Premium party equipment rentals in ${currentPage}. Select your dates to see real-time availability.` : "Select your event dates to see real-time availability for our entire North Carolina inventory.")}
          </p>
        </div>
        <div key={view + (currentPage || 'global')} className="bg-slate-900/40 rounded-[2.5rem] border border-white/5 p-4 md:p-8 min-h-[600px] shadow-inner relative overflow-hidden text-center">
          <div className="booqable-product-list"></div>
          <div className="absolute inset-0 -z-10 flex items-center justify-center opacity-20">
            <Loader2 className="w-12 h-12 animate-spin text-blue-500" />
          </div>
        </div>
      </div>
    </section>
  );

  const Navigation = () => (
    <nav className={`fixed w-full z-[100] transition-all duration-300 ${isScrolled ? 'bg-[#0f172a] shadow-2xl py-3 border-b border-white/10' : 'bg-transparent py-6'}`}>
      <div className="max-w-7xl mx-auto px-6 flex justify-between items-center gap-4">
        <div className="flex items-center gap-2 flex-shrink-0 cursor-pointer group" onClick={goHome}>
          <div className="bg-blue-600 p-2 rounded-xl shadow-lg shadow-blue-500/30 group-hover:scale-110 transition-transform">
            <Armchair className="text-white w-5 h-5 md:w-6 md:h-6" />
          </div>
          <span className="text-lg md:text-2xl font-black tracking-tighter uppercase whitespace-nowrap">
            Chairs <span className="text-blue-500">R</span> Yours
          </span>
        </div>

        <div className="hidden md:flex items-center gap-8 mx-6">
           <button onClick={() => changeView('wedding')} className={`text-[10px] font-black uppercase tracking-widest transition-colors ${view === 'wedding' ? 'text-blue-400' : 'text-zinc-400 hover:text-white'}`}>Weddings</button>
           <button onClick={() => changeView('tradeshow')} className={`text-[10px] font-black uppercase tracking-widest transition-colors ${view === 'tradeshow' ? 'text-blue-400' : 'text-zinc-400 hover:text-white'}`}>Trade Shows</button>
           <button onClick={() => changeView('plan')} className={`text-[10px] font-black uppercase tracking-widest transition-colors ${view === 'plan' ? 'text-blue-400' : 'text-zinc-400 hover:text-white'}`}>Planning Tool</button>
        </div>

        <div className="hidden lg:flex items-center gap-6">
          <div className="relative">
            <button onClick={() => setLocationMenuOpen(!locationMenuOpen)} className="text-[11px] font-black uppercase tracking-widest text-zinc-300 hover:text-white flex items-center gap-1.5 bg-white/5 px-4 py-2 rounded-full border border-white/5 whitespace-nowrap transition-all">
              <MapPin size={12} className="text-blue-500" />
              {currentPage || "Locations"} <ChevronDown size={14} className={locationMenuOpen ? 'rotate-180' : ''} />
            </button>
            {locationMenuOpen && (
              <div className="absolute top-full right-0 mt-4 w-64 bg-[#1e293b] border border-white/10 rounded-2xl overflow-hidden shadow-2xl backdrop-blur-xl p-2 grid grid-cols-1">
                <button onClick={() => navigateToLocation(null)} className="w-full text-left px-4 py-3 rounded-xl hover:bg-slate-700 text-[10px] font-black uppercase tracking-widest transition-colors">Main (All NC)</button>
                <div className="h-px bg-white/5 my-1"></div>
                {locations.map((loc) => (
                  <button key={loc} onClick={() => navigateToLocation(loc)} className={`w-full text-left px-4 py-3 rounded-xl hover:bg-blue-600 text-[10px] font-black uppercase tracking-widest transition-colors flex items-center justify-between group ${currentPage === loc ? 'bg-blue-600/20 text-blue-400' : ''}`}>
                    {loc} <ArrowRight size={12} className="opacity-0 group-hover:opacity-100 -translate-x-2 group-hover:translate-x-0 transition-all" />
                  </button>
                ))}
              </div>
            )}
          </div>
          <button onClick={scrollToInventory} className="bg-blue-600 text-white px-6 py-2.5 rounded-full hover:bg-blue-500 transition-all font-black text-[11px] uppercase tracking-widest whitespace-nowrap">Rent Online</button>
        </div>
        <button className="lg:hidden p-2 bg-white/5 rounded-lg" onClick={() => setMobileMenuOpen(true)}>
          <Menu size={20} />
        </button>
      </div>
    </nav>
  );

  const PlanPage = () => {
    const loc = getLocName();
    return (
        <div className="animate-in fade-in duration-700">
          <section className="relative pt-40 pb-16 overflow-hidden">
            <div className="absolute inset-0 z-0 opacity-20">
              <img src="https://images.unsplash.com/photo-1513151233558-d860c5398176?auto=format&fit=crop&q=80&w=2000" className="w-full h-full object-cover" alt="Event Seating Layout Planner" />
            </div>
            <div className="relative z-10 max-w-7xl mx-auto px-6 text-center">
              <h1 className="text-5xl md:text-7xl font-black mb-6 uppercase tracking-tighter leading-none">
                {loc} Seating <br /><span className="text-blue-500">& Planning Tool</span>
              </h1>
              <p className="text-zinc-400 font-bold uppercase tracking-widest text-xs max-w-xl mx-auto">
                Calculate exactly how many chairs and tables you need for your upcoming event {getSubText()}.
              </p>
            </div>
          </section>

          <section className="relative z-40 px-6 mb-24">
            <div className="max-w-7xl mx-auto space-y-8">
              <div className="bg-slate-900/90 backdrop-blur-md rounded-[3rem] border border-white/5 p-8 md:p-12 shadow-2xl">
                  <div className="grid lg:grid-cols-2 gap-12 items-start">
                      <div className="space-y-10">
                          <div>
                              <div className="flex items-center gap-3 mb-8">
                                  <div className="bg-blue-600 p-2 rounded-lg"><Users className="w-5 h-5" /></div>
                                  <h3 className="text-xl font-black uppercase tracking-tight">Guest Count</h3>
                              </div>
                              <div className="flex items-center bg-slate-800/50 rounded-2xl border border-white/5 p-4 focus-within:border-blue-500/50">
                                  <input 
                                    type="number" 
                                    min="1" 
                                    max="500"
                                    value={guestCount} 
                                    onChange={(e) => setGuestCount(Math.min(500, parseInt(e.target.value) || 0))} 
                                    className="bg-transparent text-4xl font-black text-white w-full outline-none" 
                                  />
                                  <span className="text-xs font-black text-blue-500 uppercase">Attendees</span>
                              </div>
                          </div>
                          <div>
                              <label className="text-[10px] font-black uppercase tracking-widest text-zinc-500 block mb-4">Equipment Selection</label>
                              <div className="grid grid-cols-2 gap-3">
                                  {[
                                    { id: 'round60', label: '60" Round Tables', desc: 'Fits 8 comfortably' },
                                    { id: 'round72', label: '72" Round Tables', desc: 'Fits 10-12' },
                                    { id: 'rect6', label: '6ft Folding Tables', desc: 'Standard Utility' },
                                    { id: 'rect8', label: '8ft Banquet Tables', desc: 'Great for Buffet' }
                                  ].map((t) => (
                                    <button key={t.id} onClick={() => setTableType(t.id)} className={`p-4 rounded-2xl border transition-all text-left ${tableType === t.id ? 'bg-blue-600 border-blue-400' : 'bg-slate-800/50 border-white/5 text-zinc-400 hover:bg-slate-800'}`}>
                                        <div className="font-black text-xs uppercase">{t.label}</div>
                                        <div className="text-[10px] opacity-60 font-bold">{t.desc}</div>
                                    </button>
                                  ))}
                              </div>
                          </div>
                          <div className="grid grid-cols-2 gap-6 p-6 bg-slate-800/30 rounded-2xl border border-white/5">
                              <div>
                                <div className="text-[10px] font-black text-blue-500 uppercase tracking-widest mb-1">Total Tables Needed</div>
                                <div className="text-3xl font-black">{planningData.tablesNeeded}</div>
                              </div>
                              <div>
                                <div className="text-[10px] font-black text-blue-500 uppercase tracking-widest mb-1">Total Chairs Needed</div>
                                <div className="text-3xl font-black">{planningData.chairsNeeded}</div>
                              </div>
                          </div>
                      </div>

                      {/* Visualizer Tool */}
                      <div className="bg-slate-800/40 p-10 rounded-[2.5rem] border border-white/5 flex flex-col min-h-[500px]">
                        <div className="mb-6 text-center">
                          <h4 className="text-[10px] font-black text-white/40 uppercase tracking-[0.2em] mb-2">Seating Visualizer</h4>
                          <p className="text-xs font-bold text-zinc-500 italic">Bird's-Eye Layout Preview</p>
                        </div>
                        
                        <div className="flex-1 w-full bg-[#0f172a] rounded-3xl border border-white/10 p-6 flex items-center justify-center overflow-hidden relative">
                           {/* Grid Lines Overlay */}
                           <div className="absolute inset-0 opacity-5 pointer-events-none" style={{ backgroundImage: 'radial-gradient(#fff 1px, transparent 1px)', backgroundSize: '20px 20px' }}></div>
                           
                           {/* Dynamic Furniture Render */}
                           <div className="grid grid-cols-4 sm:grid-cols-6 gap-4 w-full">
                              {Array.from({ length: planningData.visualCount }).map((_, i) => (
                                <div key={i} className="aspect-square flex flex-col items-center justify-center gap-1.5 group animate-in zoom-in duration-300">
                                   <div className={`w-8 h-8 sm:w-10 sm:h-10 border-2 transition-all group-hover:scale-110 ${planningData.isRound ? 'rounded-full' : 'rounded-md'} ${i < planningData.visualCount ? 'bg-blue-600/20 border-blue-500 shadow-lg shadow-blue-500/20' : 'bg-slate-700/50 border-white/5'}`}></div>
                                   <div className="flex gap-0.5 justify-center flex-wrap max-w-full">
                                      {Array.from({ length: 4 }).map((_, j) => (
                                         <div key={j} className="w-1.5 h-1.5 rounded-full bg-blue-400/40"></div>
                                      ))}
                                   </div>
                                </div>
                              ))}
                           </div>

                           {planningData.tablesNeeded > 48 && (
                             <div className="absolute bottom-4 left-1/2 -translate-x-1/2 bg-slate-900/90 px-4 py-2 rounded-full border border-white/10 text-[9px] font-black uppercase text-zinc-400">
                               + {planningData.tablesNeeded - 48} more tables in full layout
                             </div>
                           )}
                        </div>

                        <div className="mt-8">
                           <button onClick={scrollToInventory} className="w-full bg-blue-600 py-4 rounded-xl font-black uppercase tracking-widest text-xs hover:bg-blue-500 transition-all shadow-xl shadow-blue-500/20 flex items-center justify-center gap-3">
                             Rent this Setup <ArrowRight size={16}/>
                           </button>
                        </div>
                      </div>
                  </div>
              </div>
            </div>
          </section>
          <InventorySection title={`${loc} Planning Inventory`} />
        </div>
      );
  };

  const WeddingPage = () => {
    const loc = getLocName();
    return (
      <div className="animate-in fade-in duration-1000">
        <header className="relative pt-48 pb-32 overflow-hidden">
          <div className="absolute inset-0 z-0 opacity-30">
            <img src="https://images.unsplash.com/photo-1511795409834-ef04bbd61622?auto=format&fit=crop&q=80&w=2000" className="w-full h-full object-cover" alt="Wedding Venue Furniture" />
          </div>
          <div className="relative z-10 max-w-7xl mx-auto px-6 text-center">
            <div className="inline-flex items-center gap-2 bg-pink-500/10 border border-pink-500/20 text-pink-400 px-4 py-2 rounded-full text-[10px] font-bold uppercase tracking-widest mb-6">
              <Sparkles size={12} /> Premier Wedding Rental Services
            </div>
            <h1 className="text-5xl md:text-8xl font-black mb-8 uppercase tracking-tighter italic text-white">
               {loc} <span className="text-blue-500">Weddings.</span>
            </h1>
            <p className="text-white/60 font-bold uppercase tracking-[0.3em] text-xs">Elegant Chair Hire and Luxury Decor {getSubText()}</p>
          </div>
        </header>
        <InventorySection title={`${loc} Bridal Collections`} />
      </div>
    );
  }

  const TradeShowPage = () => {
    const loc = getLocName();
    return (
      <div className="animate-in fade-in duration-1000">
        <header className="relative pt-48 pb-32 overflow-hidden">
          <div className="absolute inset-0 z-0 opacity-20">
            <img src="https://images.unsplash.com/photo-1540575861501-7cf05a4b125a?auto=format&fit=crop&q=80&w=2000" className="w-full h-full object-cover" alt="Convention Center Trade Show Booths" />
          </div>
          <div className="relative z-10 max-w-7xl mx-auto px-6">
            <div className="inline-flex items-center gap-2 bg-blue-500/10 border border-blue-500/20 text-blue-400 px-4 py-2 rounded-full text-[10px] font-bold uppercase tracking-widest mb-6">
              Corporate Expo & Exhibition Support
            </div>
            <h1 className="text-5xl md:text-8xl font-black mb-8 uppercase tracking-tighter text-white">
              {loc} <br /><span className="text-blue-500">& Trade Shows.</span>
            </h1>
            <p className="text-white/50 font-black uppercase tracking-widest text-[10px] max-w-xl">
              Professional booth infrastructure, pipe & drape, and convention seating solutions {getSubText()}.
            </p>
          </div>
        </header>
        <InventorySection title={`${loc} Expo Equipment`} />
      </div>
    );
  }

  const HomeView = () => {
    const loc = getLocName();
    const isGlobal = !currentPage;
    
    return (
      <>
        <section className="relative min-h-screen flex flex-col items-center justify-center overflow-hidden pt-24">
          <div className="absolute inset-0 z-0 opacity-20 mix-blend-overlay">
            <img src="https://images.unsplash.com/photo-1519741497674-611481863552?auto=format&fit=crop&q=80&w=1920" className="w-full h-full object-cover" alt="Event Rental Inventory" />
          </div>
          <div className="relative z-10 w-full text-center">
            <div className="inline-flex items-center gap-2 bg-blue-500/10 border border-blue-500/20 text-blue-400 px-4 py-2 rounded-full text-[10px] font-bold uppercase tracking-[0.2em] mb-6">
              <Globe className="w-3 h-3" />
              {isGlobal ? "North Carolina's Premier Event Rental Network" : `Professional Event Infrastructure in ${loc}`}
            </div>
            <h1 className="text-4xl md:text-7xl font-black mb-6 leading-[1.1] tracking-tight uppercase text-white px-6">
              Full-Service <br />
              <span className="text-transparent bg-clip-text bg-gradient-to-r from-blue-300 via-blue-500 to-indigo-500">Equipment Rentals.</span>
            </h1>
            <p className="max-w-2xl mx-auto text-white/50 font-bold uppercase tracking-widest text-[10px] mb-12 px-6">
              {isGlobal ? "Affordable Chair Rentals, Table Rentals, and Party Supplies for North Carolina Events" : `Serving the ${loc} community with premium party and event furniture hire.`}
            </p>
            
            <div className="relative w-full max-w-5xl mx-auto mt-12 px-4 pb-20">
              <div className="bg-[#1e293b]/80 backdrop-blur-xl p-1.5 rounded-2xl border border-white/10 flex gap-1 shadow-2xl max-w-lg mx-auto mb-8">
                {[
                  { id: 'wedding', label: 'Weddings', icon: <Sparkles size={16} /> },
                  { id: 'tradeshow', label: 'Trade Shows', icon: <Briefcase size={16} /> },
                  { id: 'plan', label: 'Plan Tool', icon: <Calculator size={16} /> }
                ].map(s => (
                  <button key={s.id} onClick={() => setActiveTab(s.id)} className={`flex-1 flex items-center justify-center gap-2 py-3 px-4 rounded-xl text-[10px] md:text-xs font-black uppercase tracking-widest transition-all ${activeTab === s.id ? 'bg-blue-600 text-white shadow-lg shadow-blue-500/40' : 'text-zinc-400 hover:text-white'}`}>
                    {s.icon} <span className="hidden sm:inline">{s.label}</span>
                  </button>
                ))}
              </div>
              <div className="relative h-[400px] md:h-[500px] rounded-[3rem] overflow-hidden border border-white/10 shadow-2xl group cursor-pointer" onClick={() => changeView(activeTab)}>
                 <img src={activeTab === 'wedding' ? "https://images.unsplash.com/photo-1519225421980-715cb0215aed" : activeTab === 'tradeshow' ? "https://images.unsplash.com/photo-1540575861501-7cf05a4b125a" : "https://images.unsplash.com/photo-1513151233558-d860c5398176"} className="w-full h-full object-cover transition-transform duration-700 group-hover:scale-105" alt="Service Preview"/>
                 <div className="absolute inset-0 bg-gradient-to-t from-[#0f172a] via-[#0f172a]/40 to-transparent"></div>
                 <div className="absolute bottom-12 left-12 right-12 flex justify-between items-end">
                    <h3 className="text-3xl md:text-5xl font-black uppercase tracking-tighter leading-none text-white">{activeTab} Rentals</h3>
                    <div className="bg-white text-black p-4 rounded-full"><ArrowRight size={20}/></div>
                 </div>
              </div>
            </div>
          </div>
        </section>
        <InventorySection 
          title={isGlobal ? "Our Core Inventory" : `${loc} Event Equipment`} 
          subtitle={isGlobal ? "Quality event furniture available for delivery throughout North Carolina." : `Locally managed inventory for events in ${loc}, NC.`} 
        />
      </>
    );
  };

  return (
    <div className="min-h-screen bg-[#0f172a] text-white font-sans selection:bg-blue-500/30">
      <Navigation />
      <main>
        {view === 'wedding' && <WeddingPage />}
        {view === 'tradeshow' && <TradeShowPage />}
        {view === 'plan' && <PlanPage />}
        {view === 'home' && <HomeView />}
      </main>
      
      <footer className="bg-[#0b1120] pt-24 pb-12 border-t border-white/5 mt-24">
        <div className="max-w-7xl mx-auto px-6">
          <div className="flex flex-col md:flex-row justify-between items-start gap-12 pb-12 border-b border-white/5">
            <div>
              <div className="flex items-center gap-2 mb-6 cursor-pointer" onClick={goHome}>
                <div className="bg-blue-600 p-1.5 rounded-lg"><Armchair className="text-white w-5 h-5" /></div>
                <span className="text-xl font-black tracking-tighter uppercase whitespace-nowrap">Chairs <span className="text-blue-500">R</span> Yours</span>
              </div>
              <p className="text-zinc-500 text-xs font-bold uppercase tracking-widest max-w-xs">North Carolina's Partner for Professional Quality Party Rentals, Chair Hire, and Event Decor.</p>
            </div>
            <div className="flex gap-12">
               <div>
                  <h4 className="text-[10px] font-black uppercase tracking-[0.2em] text-white mb-4">Service Locations</h4>
                  <div className="grid grid-cols-2 gap-x-8 gap-y-2">
                    {locations.map(l => (
                      <button key={l} onClick={() => navigateToLocation(l)} className={`text-[9px] font-black uppercase text-left transition-colors ${currentPage === l ? 'text-blue-500' : 'text-zinc-500 hover:text-white'}`}>
                        {l}
                      </button>
                    ))}
                  </div>
               </div>
               <div>
                  <h4 className="text-[10px] font-black uppercase tracking-[0.2em] text-white mb-4">Contact</h4>
                  <div className="text-[9px] font-black text-zinc-500 uppercase space-y-2">
                    <div className="flex items-center gap-2"><Phone size={10} className="text-blue-500"/> 919-555-0123</div>
                    <div className="flex items-center gap-2"><Mail size={10} className="text-blue-500"/> hello@chairsryours.com</div>
                    <div className="flex items-center gap-2"><MapPin size={10} className="text-blue-500"/> Serving the Triangle Area, NC</div>
                  </div>
               </div>
            </div>
          </div>
          <div className="pt-12 flex flex-col md:flex-row justify-between items-center text-[10px] font-black text-zinc-600 uppercase tracking-widest gap-4">
            <div>© {new Date().getFullYear()} Chairs R Yours | North Carolina Event Rentals</div>
          </div>
        </div>
      </footer>
    </div>
  );
};

export default App;
