import { useState, useEffect, useRef, useCallback } from "react";

// ============================================================
// LOCALSTORAGE PERSISTENCE
// ============================================================
const STORAGE_KEY = "writerdb_data";
const loadData = () => {
  try {
    const raw = localStorage.getItem(STORAGE_KEY);
    if (raw) return JSON.parse(raw);
  } catch {}
  return { characters: [], worlds: [], sagas: [], families: [] };
};
const saveData = (data) => {
  try { localStorage.setItem(STORAGE_KEY, JSON.stringify(data)); } catch {}
};

// ============================================================
// RADAR CHART
// ============================================================
function RadarChart({ stats }) {
  const keys = ["strength","intelligence","dexterity","wisdom","speed","stamina","ps"];
  const labels = ["STR","INT","DEX","WIS","SPD","STA","PS"];
  const cx = 120, cy = 120, r = 90;
  const angle = (i) => (Math.PI * 2 * i) / keys.length - Math.PI / 2;
  const pt = (i, val) => {
    const a = angle(i); const v = (val / 20) * r;
    return [cx + v * Math.cos(a), cy + v * Math.sin(a)];
  };
  const gridPts = (frac) => keys.map((_, i) => { const a = angle(i); return `${cx + frac*r*Math.cos(a)},${cy + frac*r*Math.sin(a)}`; }).join(" ");
  const dataPoints = keys.map((k, i) => pt(i, stats[k] || 0));
  const polygon = dataPoints.map(p => p.join(",")).join(" ");
  return (
    <svg width="240" height="240" style={{filter:"drop-shadow(0 0 18px #00e5ff55)"}}>
      {[0.2,0.4,0.6,0.8,1].map(f => (
        <polygon key={f} points={gridPts(f)} fill="none" stroke="#00e5ff22" strokeWidth="1"/>
      ))}
      {keys.map((_, i) => {
        const [x1,y1] = [cx, cy];
        const a = angle(i); const [x2,y2] = [cx+r*Math.cos(a), cy+r*Math.sin(a)];
        return <line key={i} x1={x1} y1={y1} x2={x2} y2={y2} stroke="#00e5ff18" strokeWidth="1"/>;
      })}
      <polygon points={polygon} fill="#00e5ff22" stroke="#00e5ff" strokeWidth="2"/>
      {dataPoints.map(([x,y],i) => <circle key={i} cx={x} cy={y} r="4" fill="#00e5ff" stroke="#001a2e" strokeWidth="1.5"/>)}
      {keys.map((_, i) => {
        const a = angle(i); const [x,y] = [cx+(r+18)*Math.cos(a), cy+(r+18)*Math.sin(a)];
        return <text key={i} x={x} y={y} textAnchor="middle" dominantBaseline="middle" fill="#7fdbff" fontSize="11" fontFamily="'Courier New', monospace">{labels[i]}</text>;
      })}
    </svg>
  );
}

// ============================================================
// STAT SLIDER
// ============================================================
function StatSlider({ label, value, onChange }) {
  return (
    <div style={{marginBottom:10}}>
      <div style={{display:"flex",justifyContent:"space-between",marginBottom:4}}>
        <span style={{color:"#7fdbff",fontSize:12,fontFamily:"'Courier New',monospace",letterSpacing:2}}>{label}</span>
        <span style={{color:"#00e5ff",fontSize:13,fontFamily:"'Courier New',monospace",fontWeight:"bold"}}>{value}</span>
      </div>
      <div style={{position:"relative",height:6,background:"#0a1628",borderRadius:3,overflow:"visible"}}>
        <div style={{position:"absolute",left:0,top:0,height:"100%",width:`${(value/20)*100}%`,background:"linear-gradient(90deg,#0066aa,#00e5ff)",borderRadius:3,transition:"width 0.2s"}}/>
        <input type="range" min="0" max="20" value={value} onChange={e=>onChange(Number(e.target.value))}
          style={{position:"absolute",top:-5,left:0,width:"100%",opacity:0,cursor:"pointer",height:16,zIndex:2}}/>
      </div>
    </div>
  );
}

// ============================================================
// TAG INPUT
// ============================================================
function TagInput({ tags=[], onAdd, onRemove, placeholder, suggestions=[], freeText=true, freeTextVal="", onFreeTextChange }) {
  const [input, setInput] = useState("");
  const [showSug, setShowSug] = useState(false);
  const filtered = suggestions.filter(s => s.toLowerCase().includes(input.toLowerCase()) && !tags.includes(s));
  return (
    <div>
      <div style={{display:"flex",flexWrap:"wrap",gap:6,marginBottom:6}}>
        {tags.map(t => (
          <span key={t} style={{background:"#003a5c",color:"#00e5ff",border:"1px solid #00e5ff44",borderRadius:20,padding:"3px 10px",fontSize:12,display:"flex",alignItems:"center",gap:6}}>
            {t}
            <button onClick={()=>onRemove(t)} style={{background:"none",border:"none",color:"#ff6b6b",cursor:"pointer",fontSize:13,lineHeight:1,padding:0}}>×</button>
          </span>
        ))}
      </div>
      <div style={{position:"relative"}}>
        <input value={input} onChange={e=>{setInput(e.target.value);setShowSug(true);}}
          onFocus={()=>setShowSug(true)} onBlur={()=>setTimeout(()=>setShowSug(false),150)}
          onKeyDown={e=>{if(e.key==="Enter"&&input.trim()){onAdd(input.trim());setInput("");}}}
          placeholder={placeholder||"Add tag..."}
          style={{width:"100%",background:"#021020",border:"1px solid #00e5ff33",borderRadius:8,padding:"8px 12px",color:"#cce8ff",fontSize:13,fontFamily:"'Cormorant Garamond',serif",outline:"none",boxSizing:"border-box"}}/>
        {showSug && filtered.length > 0 && (
          <div style={{position:"absolute",top:"100%",left:0,right:0,background:"#061828",border:"1px solid #00e5ff33",borderRadius:8,zIndex:100,maxHeight:160,overflowY:"auto"}}>
            {filtered.map(s => (
              <div key={s} onMouseDown={()=>{onAdd(s);setInput("");setShowSug(false);}}
                style={{padding:"8px 12px",cursor:"pointer",color:"#7fdbff",fontSize:13}}
                onMouseEnter={e=>e.target.style.background="#0a2a44"}
                onMouseLeave={e=>e.target.style.background="transparent"}>{s}</div>
            ))}
          </div>
        )}
      </div>
      {freeText && (
        <textarea value={freeTextVal} onChange={e=>onFreeTextChange&&onFreeTextChange(e.target.value)}
          placeholder="Or write freely here..."
          style={{width:"100%",background:"#021020",border:"1px solid #00e5ff22",borderRadius:8,padding:"8px 12px",color:"#cce8ff",fontSize:13,fontFamily:"'Cormorant Garamond',serif",outline:"none",marginTop:6,minHeight:60,resize:"vertical",boxSizing:"border-box"}}/>
      )}
    </div>
  );
}

// ============================================================
// FIELD
// ============================================================
function Field({ label, children }) {
  return (
    <div style={{marginBottom:20}}>
      <div style={{color:"#7fdbff",fontSize:11,letterSpacing:3,textTransform:"uppercase",fontFamily:"'Courier New',monospace",marginBottom:8,opacity:0.8}}>{label}</div>
      {children}
    </div>
  );
}

function TextInput({ value, onChange, placeholder, multiline, rows=3 }) {
  const style = {width:"100%",background:"#021020",border:"1px solid #00e5ff33",borderRadius:8,padding:"9px 13px",color:"#cce8ff",fontSize:14,fontFamily:"'Cormorant Garamond',serif",outline:"none",boxSizing:"border-box",resize:"vertical"};
  if (multiline) return <textarea value={value} onChange={e=>onChange(e.target.value)} placeholder={placeholder} rows={rows} style={style}/>;
  return <input value={value} onChange={e=>onChange(e.target.value)} placeholder={placeholder} style={{...style,resize:"none"}}/>;
}

// ============================================================
// BUBBLE BACKGROUND
// ============================================================
function BubbleBG() {
  return (
    <div style={{position:"fixed",inset:0,zIndex:0,overflow:"hidden",pointerEvents:"none"}}>
      <div style={{position:"absolute",inset:0,background:"radial-gradient(ellipse at 20% 50%, #001a3388 0%, transparent 60%), radial-gradient(ellipse at 80% 20%, #00334488 0%, transparent 50%), radial-gradient(ellipse at 50% 100%, #000d2288 0%, transparent 60%),#000d1a"}}/>
      <style>{`
        @keyframes rise { 0%{transform:translateY(100vh) scale(0.5);opacity:0} 10%{opacity:0.6} 90%{opacity:0.3} 100%{transform:translateY(-10vh) scale(1.2);opacity:0}}
        @keyframes sway { 0%,100%{transform:translateX(0)} 50%{transform:translateX(30px)}}
        @keyframes shimmer { 0%,100%{opacity:0.3} 50%{opacity:0.8}}
        .bubble { position:absolute; border-radius:50%; border:1px solid #00e5ff33; animation:rise linear infinite, sway ease-in-out infinite; background:radial-gradient(circle at 30% 30%, #00e5ff18, transparent);}
      `}</style>
      {[...Array(18)].map((_,i) => (
        <div key={i} className="bubble" style={{
          width: 8+Math.random()*30, height: 8+Math.random()*30,
          left: `${Math.random()*100}%`, bottom: `-${Math.random()*20}%`,
          animationDuration: `${6+Math.random()*14}s, ${3+Math.random()*5}s`,
          animationDelay: `${Math.random()*12}s, ${Math.random()*3}s`,
        }}/>
      ))}
    </div>
  );
}

// ============================================================
// MODAL
// ============================================================
function Modal({ open, onClose, title, children, wide }) {
  if (!open) return null;
  return (
    <div style={{position:"fixed",inset:0,zIndex:1000,display:"flex",alignItems:"center",justifyContent:"center",background:"#000d1aaa",backdropFilter:"blur(4px)"}} onClick={onClose}>
      <div style={{background:"linear-gradient(160deg,#061828,#030f1e)",border:"1px solid #00e5ff33",borderRadius:16,padding:28,width:wide?"90vw":"min(90vw,560px)",maxHeight:"90vh",overflowY:"auto",position:"relative",boxShadow:"0 0 60px #00e5ff22"}} onClick={e=>e.stopPropagation()}>
        <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:20}}>
          <h2 style={{color:"#00e5ff",fontFamily:"'Cinzel',serif",fontSize:18,margin:0,letterSpacing:2}}>{title}</h2>
          <button onClick={onClose} style={{background:"none",border:"none",color:"#7fdbff",fontSize:22,cursor:"pointer",lineHeight:1}}>×</button>
        </div>
        {children}
      </div>
    </div>
  );
}

// ============================================================
// CHARACTER DISPLAY
// ============================================================
function CharacterDisplay({ char, onClose, allChars, allWorlds }) {
  const audioRef = useRef(null);
  const [loreOpen, setLoreOpen] = useState(false);
  const [relOpen, setRelOpen] = useState(false);
  useEffect(() => {
    if (char.ostUrl && audioRef.current) { audioRef.current.play().catch(()=>{}); }
    return () => { if (audioRef.current) audioRef.current.pause(); };
  }, [char.ostUrl]);

  const getOriginLabel = () => {
    if (char.originLinked) {
      const parts = [char.originLinked.continent, char.originLinked.kingdom, char.originLinked.city].filter(Boolean);
      return parts.join(" → ");
    }
    return char.origin || "Unknown";
  };

  return (
    <div style={{background:"linear-gradient(160deg,#02111f,#010b15)",border:"1px solid #00e5ff44",borderRadius:20,padding:32,maxWidth:700,margin:"0 auto",position:"relative"}}>
      {char.ostUrl && <audio ref={audioRef} src={char.ostUrl} loop style={{display:"none"}}/>}
      <div style={{display:"flex",gap:24,flexWrap:"wrap"}}>
        <div style={{textAlign:"center",minWidth:140}}>
          {char.imageUrl ? (
            <img src={char.imageUrl} alt={char.name} style={{width:140,height:140,borderRadius:12,objectFit:"cover",border:"2px solid #00e5ff44",boxShadow:"0 0 30px #00e5ff33"}}/>
          ) : (
            <div style={{width:140,height:140,borderRadius:12,background:"#0a2a44",display:"flex",alignItems:"center",justifyContent:"center",border:"2px solid #00e5ff22",color:"#003a5c",fontSize:40}}>◈</div>
          )}
          <div style={{color:"#00e5ff",fontFamily:"'Cinzel',serif",fontSize:18,marginTop:12,letterSpacing:1}}>{char.name||"Unnamed"}</div>
          {char.age && <div style={{color:"#7fdbff",fontSize:12,marginTop:4}}>Age: {char.age}</div>}
          <div style={{color:"#4499bb",fontSize:11,marginTop:2}}>{getOriginLabel()}</div>
        </div>
        <div style={{flex:1,minWidth:200}}>
          <RadarChart stats={char.stats||{}}/>
        </div>
      </div>
      <div style={{display:"flex",gap:10,flexWrap:"wrap",marginTop:20}}>
        <Btn label="📖 Character Lore" onClick={()=>setLoreOpen(true)} small/>
        <Btn label="🔗 Relationships" onClick={()=>setRelOpen(true)} small/>
      </div>
      {char.traits?.tags?.length > 0 && (
        <div style={{marginTop:16}}>
          <div style={{color:"#7fdbff",fontSize:11,letterSpacing:2,marginBottom:6}}>TRAITS</div>
          <div style={{display:"flex",flexWrap:"wrap",gap:6}}>
            {char.traits.tags.map(t=><span key={t} style={{background:"#003a5c",color:"#00e5ff",border:"1px solid #00e5ff33",borderRadius:20,padding:"3px 10px",fontSize:12}}>{t}</span>)}
          </div>
          {char.traits.free && <p style={{color:"#99ccdd",fontSize:13,marginTop:8}}>{char.traits.free}</p>}
        </div>
      )}
      {char.abilities?.tags?.length > 0 && (
        <div style={{marginTop:16}}>
          <div style={{color:"#7fdbff",fontSize:11,letterSpacing:2,marginBottom:6}}>ABILITIES</div>
          <div style={{display:"flex",flexWrap:"wrap",gap:6}}>
            {char.abilities.tags.map(t=><span key={t} style={{background:"#001a33",color:"#44bbff",border:"1px solid #44bbff33",borderRadius:20,padding:"3px 10px",fontSize:12}}>{t}</span>)}
          </div>
          {char.abilities.free && <p style={{color:"#99ccdd",fontSize:13,marginTop:8}}>{char.abilities.free}</p>}
        </div>
      )}
      {char.ostName && <div style={{marginTop:12,color:"#4499bb",fontSize:12}}>🎵 {char.ostName}</div>}

      <Modal open={loreOpen} onClose={()=>setLoreOpen(false)} title="Character Lore">
        <div style={{color:"#cce8ff",fontSize:14,lineHeight:1.8,fontFamily:"'Cormorant Garamond',serif",whiteSpace:"pre-wrap"}}>{char.lore||"No lore written yet."}</div>
      </Modal>
      <Modal open={relOpen} onClose={()=>setRelOpen(false)} title="Relationships">
        {(char.relationships||[]).length === 0 ? <p style={{color:"#4499bb"}}>No relationships added.</p> :
          (char.relationships||[]).map((rel,i) => {
            const linked = allChars.find(c=>c.id===rel.charId);
            return (
              <div key={i} style={{display:"flex",alignItems:"center",gap:12,marginBottom:12,padding:"10px 14px",background:"#0a1e30",borderRadius:10,border:"1px solid #00e5ff22"}}>
                {linked?.imageUrl ? <img src={linked.imageUrl} style={{width:40,height:40,borderRadius:8,objectFit:"cover"}}/> : <div style={{width:40,height:40,borderRadius:8,background:"#0a2a44",display:"flex",alignItems:"center",justifyContent:"center",color:"#003a5c"}}>◈</div>}
                <div>
                  <div style={{color:"#7fdbff",fontSize:11,letterSpacing:2}}>{rel.label}</div>
                  <div style={{color:"#00e5ff",fontSize:14}}>{linked?.name||rel.charId}</div>
                </div>
              </div>
            );
          })
        }
      </Modal>
    </div>
  );
}

// ============================================================
// CHARACTER EDITOR
// ============================================================
function CharacterEditor({ initial, onSave, onCancel, allChars, allWorlds }) {
  const blank = { id: Date.now().toString(), name:"", age:"", origin:"", originLinked:null, stats:{strength:10,intelligence:10,dexterity:10,wisdom:10,speed:10,stamina:10,ps:10}, traits:{tags:[],free:""}, abilities:{tags:[],free:""}, lore:"", relationships:[], ostUrl:null, ostName:"", imageUrl:null };
  const [c, setC] = useState(initial || blank);
  const [showDisplay, setShowDisplay] = useState(false);
  const imgRef = useRef(), ostRef = useRef();

  const set = (key, val) => setC(prev => ({...prev, [key]: val}));
  const setStat = (k, v) => setC(prev => ({...prev, stats:{...prev.stats,[k]:v}}));
  const setTagField = (field, type, val) => setC(prev => ({...prev, [field]:{...prev[field],[type]:val}}));

  const handleImage = e => {
    const f = e.target.files[0]; if(!f) return;
    const url = URL.createObjectURL(f); set("imageUrl", url);
  };
  const handleOst = e => {
    const f = e.target.files[0]; if(!f) return;
    const url = URL.createObjectURL(f); set("ostUrl", url); set("ostName", f.name);
  };

  const addRel = () => set("relationships", [...(c.relationships||[]), {label:"", charId:""}]);
  const setRel = (i, key, val) => {
    const r = [...(c.relationships||[])]; r[i] = {...r[i],[key]:val}; set("relationships", r);
  };
  const removeRel = i => set("relationships", (c.relationships||[]).filter((_,idx)=>idx!==i));

  // World origin options
  const worldOriginOptions = [];
  (allWorlds||[]).forEach(w => {
    (w.continents||[]).forEach(cont => {
      (cont.kingdoms||[]).forEach(k => {
        (k.cities||[]).forEach(city => {
          worldOriginOptions.push({label:`${cont.name} → ${k.name} → ${city.name}`, value:{continent:cont.name,kingdom:k.name,city:city.name}});
        });
        worldOriginOptions.push({label:`${cont.name} → ${k.name}`, value:{continent:cont.name,kingdom:k.name,city:""}});
      });
      worldOriginOptions.push({label:cont.name, value:{continent:cont.name,kingdom:"",city:""}});
    });
  });

  // Ability suggestions from world power systems
  const abilitySuggestions = [];
  (allWorlds||[]).forEach(w => {
    (w.powerSystems||[]).forEach(ps => {
      (ps.abilities||[]).forEach(ab => abilitySuggestions.push(ab.name));
    });
  });

  return (
    <div style={{color:"#cce8ff"}}>
      {showDisplay && (
        <Modal open wide onClose={()=>setShowDisplay(false)} title="Character Display">
          <CharacterDisplay char={c} allChars={allChars} allWorlds={allWorlds}/>
        </Modal>
      )}
      <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:20}}>
        {/* LEFT */}
        <div>
          <Field label="Character Image">
            <div style={{display:"flex",alignItems:"center",gap:12}}>
              {c.imageUrl ? <img src={c.imageUrl} style={{width:80,height:80,borderRadius:10,objectFit:"cover",border:"2px solid #00e5ff33"}}/> : <div style={{width:80,height:80,borderRadius:10,background:"#0a1628",border:"2px dashed #00e5ff22",display:"flex",alignItems:"center",justifyContent:"center",color:"#003a5c",fontSize:24}}>+</div>}
              <input type="file" accept="image/*" ref={imgRef} onChange={handleImage} style={{display:"none"}}/>
              <Btn label="Upload Image" onClick={()=>imgRef.current.click()} small/>
            </div>
          </Field>
          <Field label="Name"><TextInput value={c.name} onChange={v=>set("name",v)} placeholder="Character name..."/></Field>
          <Field label="Age"><TextInput value={c.age} onChange={v=>set("age",v)} placeholder="Age..."/></Field>
          <Field label="Origin">
            <select value={c.originLinked ? JSON.stringify(c.originLinked) : ""} onChange={e=>{
              if(e.target.value) set("originLinked",JSON.parse(e.target.value)); else set("originLinked",null);
            }} style={{width:"100%",background:"#021020",border:"1px solid #00e5ff33",borderRadius:8,padding:"8px 12px",color:"#cce8ff",fontSize:13,marginBottom:6,outline:"none"}}>
              <option value="">— Link from World —</option>
              {worldOriginOptions.map((o,i)=><option key={i} value={JSON.stringify(o.value)}>{o.label}</option>)}
            </select>
            {!c.originLinked && <TextInput value={c.origin} onChange={v=>set("origin",v)} placeholder="Or write origin manually..."/>}
            {c.originLinked && <div style={{color:"#4499bb",fontSize:12,marginTop:4}}>✓ Linked: {[c.originLinked.continent,c.originLinked.kingdom,c.originLinked.city].filter(Boolean).join(" → ")}</div>}
          </Field>
          <Field label="OST / Music">
            <input type="file" accept="audio/*" ref={ostRef} onChange={handleOst} style={{display:"none"}}/>
            <Btn label={c.ostName ? `🎵 ${c.ostName}` : "Upload OST"} onClick={()=>ostRef.current.click()} small/>
          </Field>
        </div>
        {/* RIGHT - STATS */}
        <div>
          <Field label="Stats">
            {["strength","intelligence","dexterity","wisdom","speed","stamina","ps"].map(k => (
              <StatSlider key={k} label={k.toUpperCase()} value={c.stats[k]||0} onChange={v=>setStat(k,v)}/>
            ))}
          </Field>
          <div style={{display:"flex",justifyContent:"center"}}>
            <RadarChart stats={c.stats}/>
          </div>
        </div>
      </div>

      <Field label="Character Traits">
        <TagInput tags={c.traits?.tags||[]} onAdd={t=>setTagField("traits","tags",[...(c.traits?.tags||[]),t])} onRemove={t=>setTagField("traits","tags",(c.traits?.tags||[]).filter(x=>x!==t))} freeTextVal={c.traits?.free||""} onFreeTextChange={v=>setTagField("traits","free",v)} placeholder="Add trait tag..."/>
      </Field>

      <Field label="Abilities">
        <TagInput tags={c.abilities?.tags||[]} suggestions={abilitySuggestions} onAdd={t=>setTagField("abilities","tags",[...(c.abilities?.tags||[]),t])} onRemove={t=>setTagField("abilities","tags",(c.abilities?.tags||[]).filter(x=>x!==t))} freeTextVal={c.abilities?.free||""} onFreeTextChange={v=>setTagField("abilities","free",v)} placeholder="Add ability..."/>
      </Field>

      <Field label="Character Lore">
        <TextInput value={c.lore||""} onChange={v=>set("lore",v)} placeholder="Write the character's lore, backstory, history..." multiline rows={5}/>
      </Field>

      <Field label="Relationships">
        {(c.relationships||[]).map((rel,i) => (
          <div key={i} style={{display:"flex",gap:8,marginBottom:8,alignItems:"center"}}>
            <input value={rel.label} onChange={e=>setRel(i,"label",e.target.value)} placeholder="Label (e.g. Father)" style={{flex:"0 0 140px",background:"#021020",border:"1px solid #00e5ff33",borderRadius:8,padding:"8px 10px",color:"#cce8ff",fontSize:13,outline:"none"}}/>
            <select value={rel.charId} onChange={e=>setRel(i,"charId",e.target.value)} style={{flex:1,background:"#021020",border:"1px solid #00e5ff33",borderRadius:8,padding:"8px 10px",color:"#cce8ff",fontSize:13,outline:"none"}}>
              <option value="">— Select character —</option>
              {(allChars||[]).filter(ch=>ch.id!==c.id).map(ch=><option key={ch.id} value={ch.id}>{ch.name||"Unnamed"}</option>)}
            </select>
            <button onClick={()=>removeRel(i)} style={{background:"none",border:"1px solid #ff6b6b44",borderRadius:6,color:"#ff6b6b",cursor:"pointer",padding:"6px 10px",fontSize:13}}>×</button>
          </div>
        ))}
        <Btn label="+ Add Relationship" onClick={addRel} small ghost/>
      </Field>

      <div style={{display:"flex",gap:10,justifyContent:"flex-end",marginTop:20,flexWrap:"wrap"}}>
        <Btn label="👁 Display" onClick={()=>setShowDisplay(true)} small ghost/>
        <Btn label="Cancel" onClick={onCancel} small ghost/>
        <Btn label="Save Character" onClick={()=>onSave(c)} small/>
      </div>
    </div>
  );
}

// ============================================================
// FAMILY TREE NODE
// ============================================================
function FamilyTreeNode({ node, allChars, onUpdate, depth=0 }) {
  const char = allChars.find(c => c.id === node.charId);
  const [addingCouple, setAddingCouple] = useState(false);
  const [addingChild, setAddingChild] = useState(null);

  const addCouple = (coupleCfg) => {
    const updated = {...node, couples:[...(node.couples||[]), {fatherId:coupleCfg.fatherId, motherId:coupleCfg.motherId, children:[]}]};
    onUpdate(updated);
    setAddingCouple(false);
  };
  const addChild = (coupleIdx, childId) => {
    const couples = [...(node.couples||[])];
    couples[coupleIdx] = {...couples[coupleIdx], children:[...(couples[coupleIdx].children||[]), {charId:childId, couples:[]}]};
    onUpdate({...node, couples});
    setAddingChild(null);
  };
  const updateChild = (coupleIdx, childIdx, childNode) => {
    const couples = [...(node.couples||[])];
    const children = [...couples[coupleIdx].children];
    children[childIdx] = childNode;
    couples[coupleIdx] = {...couples[coupleIdx], children};
    onUpdate({...node, couples});
  };

  return (
    <div style={{display:"flex",flexDirection:"column",alignItems:"center",position:"relative"}}>
      {/* Character card */}
      <div style={{background:"#0a1e30",border:"2px solid #00e5ff44",borderRadius:12,padding:"10px 14px",textAlign:"center",minWidth:100,position:"relative",zIndex:1}}>
        {char?.imageUrl ? <img src={char.imageUrl} style={{width:52,height:52,borderRadius:8,objectFit:"cover",marginBottom:6,border:"1px solid #00e5ff33"}}/> : <div style={{width:52,height:52,borderRadius:8,background:"#0a2a44",display:"flex",alignItems:"center",justifyContent:"center",color:"#003a5c",fontSize:20,margin:"0 auto 6px"}}>◈</div>}
        <div style={{color:"#00e5ff",fontSize:12,fontFamily:"'Cinzel',serif"}}>{char?.name||"Unknown"}</div>
        {!node.isUnknown && <button onClick={()=>setAddingCouple(true)} style={{marginTop:6,background:"none",border:"1px solid #00e5ff44",borderRadius:6,color:"#7fdbff",cursor:"pointer",fontSize:11,padding:"3px 8px"}}>+ Couple</button>}
      </div>

      {/* Couples & children */}
      {(node.couples||[]).length > 0 && (
        <div style={{marginTop:16,display:"flex",gap:24,flexWrap:"wrap",justifyContent:"center"}}>
          {(node.couples||[]).map((couple, ci) => {
            const other = couple.fatherId===node.charId ? allChars.find(c=>c.id===couple.motherId) : allChars.find(c=>c.id===couple.fatherId);
            return (
              <div key={ci} style={{display:"flex",flexDirection:"column",alignItems:"center"}}>
                {/* Partner */}
                <div style={{display:"flex",gap:10,alignItems:"center",marginBottom:8}}>
                  <div style={{width:2,height:20,background:"#00e5ff44",margin:"0 auto"}}/>
                  <div style={{background:"#061828",border:"1px solid #00e5ff33",borderRadius:8,padding:"6px 10px",textAlign:"center",minWidth:80}}>
                    {other?.imageUrl ? <img src={other.imageUrl} style={{width:36,height:36,borderRadius:6,objectFit:"cover",marginBottom:4}}/> : <div style={{width:36,height:36,borderRadius:6,background:"#0a2a44",display:"flex",alignItems:"center",justifyContent:"center",color:"#003a5c",fontSize:16,margin:"0 auto 4px"}}>◈</div>}
                    <div style={{color:"#7fdbff",fontSize:11}}>{other?.name||"Unknown"}</div>
                  </div>
                </div>
                {/* Children */}
                <div style={{display:"flex",gap:12,flexWrap:"wrap",justifyContent:"center"}}>
                  {(couple.children||[]).map((child, childIdx) => (
                    <FamilyTreeNode key={childIdx} node={child} allChars={allChars} depth={depth+1}
                      onUpdate={cn=>updateChild(ci,childIdx,cn)}/>
                  ))}
                  <button onClick={()=>setAddingChild(ci)} style={{background:"none",border:"1px dashed #00e5ff44",borderRadius:10,color:"#7fdbff",cursor:"pointer",fontSize:20,width:52,height:52,marginTop:24}}>+</button>
                </div>
                {addingChild===ci && (
                  <div style={{marginTop:8}}>
                    <select defaultValue="" onChange={e=>{if(e.target.value)addChild(ci,e.target.value);}} style={{background:"#021020",border:"1px solid #00e5ff33",borderRadius:8,padding:"6px 10px",color:"#cce8ff",fontSize:12,outline:"none"}}>
                      <option value="">Select child character</option>
                      <option value="unknown">Unknown</option>
                      {allChars.map(ch=><option key={ch.id} value={ch.id}>{ch.name||"Unnamed"}</option>)}
                    </select>
                    <button onClick={()=>setAddingChild(null)} style={{marginLeft:6,background:"none",border:"none",color:"#ff6b6b",cursor:"pointer"}}>×</button>
                  </div>
                )}
              </div>
            );
          })}
        </div>
      )}

      {/* Add couple modal */}
      {addingCouple && (
        <AddCoupleModal allChars={allChars} rootCharId={node.charId} onAdd={addCouple} onClose={()=>setAddingCouple(false)}/>
      )}
    </div>
  );
}

function AddCoupleModal({ allChars, rootCharId, onAdd, onClose }) {
  const [partner, setPartner] = useState("");
  return (
    <div style={{position:"fixed",inset:0,zIndex:2000,display:"flex",alignItems:"center",justifyContent:"center",background:"#000d1acc"}} onClick={onClose}>
      <div style={{background:"#061828",border:"1px solid #00e5ff33",borderRadius:14,padding:24,minWidth:300}} onClick={e=>e.stopPropagation()}>
        <h3 style={{color:"#00e5ff",fontFamily:"'Cinzel',serif",margin:"0 0 16px",fontSize:16}}>Add Partner</h3>
        <select value={partner} onChange={e=>setPartner(e.target.value)} style={{width:"100%",background:"#021020",border:"1px solid #00e5ff33",borderRadius:8,padding:"9px 12px",color:"#cce8ff",fontSize:13,outline:"none",marginBottom:14}}>
          <option value="">Select partner...</option>
          <option value="unknown">Unknown</option>
          {allChars.filter(c=>c.id!==rootCharId).map(c=><option key={c.id} value={c.id}>{c.name||"Unnamed"}</option>)}
        </select>
        <div style={{display:"flex",gap:8,justifyContent:"flex-end"}}>
          <Btn label="Cancel" onClick={onClose} small ghost/>
          <Btn label="Add" onClick={()=>{if(partner)onAdd({fatherId:rootCharId,motherId:partner==="unknown"?"unknown":partner});}} small/>
        </div>
      </div>
    </div>
  );
}

// ============================================================
// WORLD EDITOR
// ============================================================
function WorldEditor({ initial, onSave, onCancel }) {
  const blank = { id: Date.now().toString(), name:"", continents:[], powerSystems:[] };
  const [w, setW] = useState(initial||blank);

  const addContinent = () => setW(p=>({...p,continents:[...p.continents,{id:Date.now().toString(),name:"",kingdoms:[]}]}));
  const updateContinent = (i,cont) => setW(p=>{const c=[...p.continents];c[i]=cont;return{...p,continents:c};});
  const removeContinent = (i) => setW(p=>({...p,continents:p.continents.filter((_,idx)=>idx!==i)}));
  const addPS = () => setW(p=>({...p,powerSystems:[...p.powerSystems,{id:Date.now().toString(),name:"",description:"",abilities:[]}]}));
  const updatePS = (i,ps) => setW(p=>{const a=[...p.powerSystems];a[i]=ps;return{...p,powerSystems:a};});
  const removePS = (i) => setW(p=>({...p,powerSystems:p.powerSystems.filter((_,idx)=>idx!==i)}));

  return (
    <div>
      <Field label="World Name"><TextInput value={w.name} onChange={v=>setW(p=>({...p,name:v}))} placeholder="World name..."/></Field>
      <Field label="Continents">
        {w.continents.map((cont,ci)=>(
          <ContinentEditor key={cont.id} cont={cont} onChange={c=>updateContinent(ci,c)} onRemove={()=>removeContinent(ci)}/>
        ))}
        <Btn label="+ Add Continent" onClick={addContinent} small ghost/>
      </Field>
      <Field label="Power Systems">
        {w.powerSystems.map((ps,pi)=>(
          <PSEditor key={ps.id} ps={ps} onChange={p=>updatePS(pi,p)} onRemove={()=>removePS(pi)}/>
        ))}
        <Btn label="+ Add Power System" onClick={addPS} small ghost/>
      </Field>
      <div style={{display:"flex",gap:10,justifyContent:"flex-end",marginTop:16}}>
        <Btn label="Cancel" onClick={onCancel} small ghost/>
        <Btn label="Save World" onClick={()=>onSave(w)} small/>
      </div>
    </div>
  );
}

function ContinentEditor({ cont, onChange, onRemove }) {
  const addKingdom = () => onChange({...cont,kingdoms:[...cont.kingdoms,{id:Date.now().toString(),name:"",cities:[]}]});
  const updateKingdom = (i,k) => { const ks=[...cont.kingdoms];ks[i]=k;onChange({...cont,kingdoms:ks}); };
  const removeKingdom = (i) => onChange({...cont,kingdoms:cont.kingdoms.filter((_,idx)=>idx!==i)});
  return (
    <div style={{background:"#061828",border:"1px solid #00e5ff22",borderRadius:10,padding:14,marginBottom:10}}>
      <div style={{display:"flex",gap:8,marginBottom:8}}>
        <input value={cont.name} onChange={e=>onChange({...cont,name:e.target.value})} placeholder="Continent name..." style={{flex:1,background:"#021020",border:"1px solid #00e5ff33",borderRadius:8,padding:"7px 10px",color:"#cce8ff",fontSize:13,outline:"none"}}/>
        <button onClick={onRemove} style={{background:"none",border:"1px solid #ff6b6b44",borderRadius:6,color:"#ff6b6b",cursor:"pointer",padding:"6px 10px"}}>×</button>
      </div>
      {cont.kingdoms.map((k,ki)=>(
        <KingdomEditor key={k.id} kingdom={k} onChange={kk=>updateKingdom(ki,kk)} onRemove={()=>removeKingdom(ki)}/>
      ))}
      <Btn label="+ Kingdom/Country" onClick={addKingdom} small ghost/>
    </div>
  );
}

function KingdomEditor({ kingdom, onChange, onRemove }) {
  const addCity = () => onChange({...kingdom,cities:[...kingdom.cities,{id:Date.now().toString(),name:""}]});
  const updateCity = (i,city) => { const cs=[...kingdom.cities];cs[i]=city;onChange({...kingdom,cities:cs}); };
  const removeCity = (i) => onChange({...kingdom,cities:kingdom.cities.filter((_,idx)=>idx!==i)});
  return (
    <div style={{background:"#030f1e",border:"1px solid #00e5ff18",borderRadius:8,padding:10,marginBottom:8,marginLeft:14}}>
      <div style={{display:"flex",gap:8,marginBottom:6}}>
        <input value={kingdom.name} onChange={e=>onChange({...kingdom,name:e.target.value})} placeholder="Kingdom/Country name..." style={{flex:1,background:"#021020",border:"1px solid #00e5ff22",borderRadius:6,padding:"6px 10px",color:"#cce8ff",fontSize:12,outline:"none"}}/>
        <button onClick={onRemove} style={{background:"none",border:"none",color:"#ff6b6b44",cursor:"pointer",fontSize:16}}>×</button>
      </div>
      {kingdom.cities.map((city,ci)=>(
        <div key={city.id} style={{display:"flex",gap:6,marginBottom:4,marginLeft:12}}>
          <input value={city.name} onChange={e=>updateCity(ci,{...city,name:e.target.value})} placeholder="Village/City name..." style={{flex:1,background:"#021020",border:"1px solid #00e5ff18",borderRadius:6,padding:"5px 8px",color:"#cce8ff",fontSize:12,outline:"none"}}/>
          <button onClick={()=>removeCity(ci)} style={{background:"none",border:"none",color:"#ff6b6b44",cursor:"pointer"}}>×</button>
        </div>
      ))}
      <Btn label="+ Village/City" onClick={addCity} small ghost/>
    </div>
  );
}

function PSEditor({ ps, onChange, onRemove }) {
  const addAbility = () => onChange({...ps,abilities:[...ps.abilities,{id:Date.now().toString(),name:"",description:""}]});
  const updateAbility = (i,ab) => { const a=[...ps.abilities];a[i]=ab;onChange({...ps,abilities:a}); };
  const removeAbility = (i) => onChange({...ps,abilities:ps.abilities.filter((_,idx)=>idx!==i)});
  return (
    <div style={{background:"#061828",border:"1px solid #00e5ff22",borderRadius:10,padding:14,marginBottom:10}}>
      <div style={{display:"flex",gap:8,marginBottom:8}}>
        <input value={ps.name} onChange={e=>onChange({...ps,name:e.target.value})} placeholder="Power system name..." style={{flex:1,background:"#021020",border:"1px solid #00e5ff33",borderRadius:8,padding:"7px 10px",color:"#cce8ff",fontSize:13,outline:"none"}}/>
        <button onClick={onRemove} style={{background:"none",border:"1px solid #ff6b6b44",borderRadius:6,color:"#ff6b6b",cursor:"pointer",padding:"6px 10px"}}>×</button>
      </div>
      <textarea value={ps.description} onChange={e=>onChange({...ps,description:e.target.value})} placeholder="Power system description..." rows={2} style={{width:"100%",background:"#021020",border:"1px solid #00e5ff22",borderRadius:8,padding:"7px 10px",color:"#cce8ff",fontSize:12,outline:"none",resize:"vertical",boxSizing:"border-box",marginBottom:8}}/>
      {ps.abilities.map((ab,ai)=>(
        <div key={ab.id} style={{display:"flex",gap:6,marginBottom:6}}>
          <input value={ab.name} onChange={e=>updateAbility(ai,{...ab,name:e.target.value})} placeholder="Ability name..." style={{flex:"0 0 150px",background:"#021020",border:"1px solid #00e5ff22",borderRadius:6,padding:"6px 8px",color:"#cce8ff",fontSize:12,outline:"none"}}/>
          <input value={ab.description||""} onChange={e=>updateAbility(ai,{...ab,description:e.target.value})} placeholder="Description..." style={{flex:1,background:"#021020",border:"1px solid #00e5ff22",borderRadius:6,padding:"6px 8px",color:"#cce8ff",fontSize:12,outline:"none"}}/>
          <button onClick={()=>removeAbility(ai)} style={{background:"none",border:"none",color:"#ff6b6b44",cursor:"pointer"}}>×</button>
        </div>
      ))}
      <Btn label="+ Add Ability" onClick={addAbility} small ghost/>
    </div>
  );
}

// ============================================================
// SAGA EDITOR
// ============================================================
function SagaEditor({ initial, onSave, onCancel, allChars, allWorlds }) {
  const blank = { id:Date.now().toString(), name:"", continent:"", powerSystem:"", description:"", acts:[] };
  const [s, setS] = useState(initial||blank);

  const addAct = () => setS(p=>({...p,acts:[...p.acts,{id:Date.now().toString(),name:"",villains:[],description:""}]}));
  const updateAct = (i,act) => { const a=[...s.acts];a[i]=act;setS(p=>({...p,acts:a})); };
  const removeAct = (i) => setS(p=>({...p,acts:p.acts.filter((_,idx)=>idx!==i)}));

  const worldNames = (allWorlds||[]).map(w=>w.name);
  const continentOptions = [];
  (allWorlds||[]).forEach(w=>(w.continents||[]).forEach(c=>continentOptions.push(c.name)));
  const psOptions = [];
  (allWorlds||[]).forEach(w=>(w.powerSystems||[]).forEach(ps=>psOptions.push(ps.name)));

  return (
    <div>
      <Field label="Saga Name"><TextInput value={s.name} onChange={v=>setS(p=>({...p,name:v}))} placeholder="Saga name..."/></Field>
      <Field label="Description"><TextInput value={s.description||""} onChange={v=>setS(p=>({...p,description:v}))} placeholder="Describe this saga..." multiline rows={3}/></Field>
      <div style={{display:"grid",gridTemplateColumns:"1fr 1fr",gap:14}}>
        <Field label="Continent">
          <select value={s.continent} onChange={e=>setS(p=>({...p,continent:e.target.value}))} style={{width:"100%",background:"#021020",border:"1px solid #00e5ff33",borderRadius:8,padding:"8px 12px",color:"#cce8ff",fontSize:13,outline:"none"}}>
            <option value="">Select continent...</option>
            {continentOptions.map(c=><option key={c} value={c}>{c}</option>)}
          </select>
        </Field>
        <Field label="Power System">
          <select value={s.powerSystem} onChange={e=>setS(p=>({...p,powerSystem:e.target.value}))} style={{width:"100%",background:"#021020",border:"1px solid #00e5ff33",borderRadius:8,padding:"8px 12px",color:"#cce8ff",fontSize:13,outline:"none"}}>
            <option value="">Select power system...</option>
            {psOptions.map(ps=><option key={ps} value={ps}>{ps}</option>)}
          </select>
        </Field>
      </div>
      <Field label="Acts">
        {s.acts.map((act,ai)=>(
          <ActEditor key={act.id} act={act} allChars={allChars} onChange={a=>updateAct(ai,a)} onRemove={()=>removeAct(ai)}/>
        ))}
        <Btn label="+ Add Act" onClick={addAct} small ghost/>
      </Field>
      <div style={{display:"flex",gap:10,justifyContent:"flex-end",marginTop:16}}>
        <Btn label="Cancel" onClick={onCancel} small ghost/>
        <Btn label="Save Saga" onClick={()=>onSave(s)} small/>
      </div>
    </div>
  );
}

function ActEditor({ act, onChange, onRemove, allChars }) {
  const addVillain = (id) => { if(id&&!act.villains.includes(id)) onChange({...act,villains:[...act.villains,id]}); };
  const removeVillain = (id) => onChange({...act,villains:act.villains.filter(v=>v!==id)});
  return (
    <div style={{background:"#061828",border:"1px solid #00e5ff22",borderRadius:10,padding:14,marginBottom:10}}>
      <div style={{display:"flex",gap:8,marginBottom:10}}>
        <input value={act.name} onChange={e=>onChange({...act,name:e.target.value})} placeholder="Act name..." style={{flex:1,background:"#021020",border:"1px solid #00e5ff33",borderRadius:8,padding:"7px 10px",color:"#cce8ff",fontSize:13,outline:"none"}}/>
        <button onClick={onRemove} style={{background:"none",border:"1px solid #ff6b6b44",borderRadius:6,color:"#ff6b6b",cursor:"pointer",padding:"6px 10px"}}>×</button>
      </div>
      <textarea value={act.description||""} onChange={e=>onChange({...act,description:e.target.value})} placeholder="Write what happens in this act..." rows={3} style={{width:"100%",background:"#021020",border:"1px solid #00e5ff22",borderRadius:8,padding:"7px 10px",color:"#cce8ff",fontSize:12,outline:"none",resize:"vertical",boxSizing:"border-box",marginBottom:8}}/>
      <div style={{color:"#7fdbff",fontSize:11,letterSpacing:2,marginBottom:6}}>VILLAINS</div>
      <div style={{display:"flex",flexWrap:"wrap",gap:6,marginBottom:8}}>
        {(act.villains||[]).map(vid=>{
          const ch=allChars.find(c=>c.id===vid);
          return ch ? <span key={vid} style={{background:"#1a0a0a",color:"#ff6b6b",border:"1px solid #ff6b6b44",borderRadius:20,padding:"3px 10px",fontSize:12,display:"flex",alignItems:"center",gap:6}}>
            {ch.name} <button onClick={()=>removeVillain(vid)} style={{background:"none",border:"none",color:"#ff6b6b",cursor:"pointer",fontSize:13,padding:0}}>×</button>
          </span> : null;
        })}
      </div>
      <select defaultValue="" onChange={e=>{if(e.target.value)addVillain(e.target.value);}} style={{background:"#021020",border:"1px solid #00e5ff33",borderRadius:8,padding:"7px 10px",color:"#cce8ff",fontSize:12,outline:"none"}}>
        <option value="">+ Add villain character...</option>
        {allChars.map(ch=><option key={ch.id} value={ch.id}>{ch.name||"Unnamed"}</option>)}
      </select>
    </div>
  );
}

// ============================================================
// BUTTON
// ============================================================
function Btn({ label, onClick, small, ghost, danger }) {
  return (
    <button onClick={onClick} style={{
      background: ghost ? "transparent" : danger ? "#1a0a0a" : "linear-gradient(135deg,#004488,#006699)",
      border: ghost ? "1px solid #00e5ff44" : danger ? "1px solid #ff6b6b44" : "1px solid #00e5ff66",
      borderRadius: small ? 8 : 10,
      color: danger ? "#ff6b6b" : "#00e5ff",
      cursor:"pointer",
      padding: small ? "8px 16px" : "11px 22px",
      fontSize: small ? 13 : 14,
      fontFamily:"'Cinzel',serif",
      letterSpacing: 1,
      transition:"all 0.2s",
      whiteSpace:"nowrap",
    }}
    onMouseEnter={e=>{e.currentTarget.style.background=ghost?"#00e5ff11":danger?"#330a0a":"linear-gradient(135deg,#005599,#0088bb)";}}
    onMouseLeave={e=>{e.currentTarget.style.background=ghost?"transparent":danger?"#1a0a0a":"linear-gradient(135deg,#004488,#006699)";}}
    >{label}</button>
  );
}

// ============================================================
// CARD
// ============================================================
function Card({ char, onClick, onEdit, onDelete }) {
  return (
    <div style={{background:"linear-gradient(160deg,#061828,#030f1e)",border:"1px solid #00e5ff22",borderRadius:14,padding:16,cursor:"pointer",transition:"all 0.2s",position:"relative"}}
      onMouseEnter={e=>{e.currentTarget.style.border="1px solid #00e5ff66";e.currentTarget.style.boxShadow="0 0 20px #00e5ff11";}}
      onMouseLeave={e=>{e.currentTarget.style.border="1px solid #00e5ff22";e.currentTarget.style.boxShadow="none";}}>
      <div onClick={onClick} style={{display:"flex",gap:12,alignItems:"center"}}>
        {char.imageUrl ? <img src={char.imageUrl} style={{width:52,height:52,borderRadius:9,objectFit:"cover",border:"1px solid #00e5ff33",flexShrink:0}}/> : <div style={{width:52,height:52,borderRadius:9,background:"#0a1628",display:"flex",alignItems:"center",justifyContent:"center",color:"#003a5c",fontSize:22,flexShrink:0}}>◈</div>}
        <div>
          <div style={{color:"#00e5ff",fontFamily:"'Cinzel',serif",fontSize:15}}>{char.name||"Unnamed"}</div>
          {char.age && <div style={{color:"#4499bb",fontSize:12}}>Age {char.age}</div>}
          {(char.originLinked||char.origin) && <div style={{color:"#33667788",fontSize:11}}>{char.originLinked ? [char.originLinked.continent,char.originLinked.kingdom,char.originLinked.city].filter(Boolean).join(" · ") : char.origin}</div>}
        </div>
      </div>
      <div style={{position:"absolute",top:10,right:10,display:"flex",gap:6}}>
        <button onClick={e=>{e.stopPropagation();onEdit();}} style={{background:"none",border:"1px solid #00e5ff22",borderRadius:6,color:"#7fdbff",cursor:"pointer",padding:"3px 8px",fontSize:11}}>Edit</button>
        <button onClick={e=>{e.stopPropagation();onDelete();}} style={{background:"none",border:"1px solid #ff6b6b22",borderRadius:6,color:"#ff6b6b88",cursor:"pointer",padding:"3px 8px",fontSize:11}}>×</button>
      </div>
    </div>
  );
}

// ============================================================
// MAIN APP
// ============================================================
export default function App() {
  const [data, setData] = useState(loadData);
  const [tab, setTab] = useState("characters");
  const [modal, setModal] = useState(null); // {type, payload}
  const [viewing, setViewing] = useState(null);

  useEffect(() => { saveData(data); }, [data]);

  // Characters
  const saveChar = (c) => {
    setData(d => {
      const exists = d.characters.find(x=>x.id===c.id);
      return {...d, characters: exists ? d.characters.map(x=>x.id===c.id?c:x) : [...d.characters, c]};
    });
    setModal(null);
  };
  const deleteChar = (id) => setData(d=>({...d,characters:d.characters.filter(c=>c.id!==id)}));

  // Worlds
  const saveWorld = (w) => {
    setData(d => {
      const exists = d.worlds.find(x=>x.id===w.id);
      return {...d, worlds: exists ? d.worlds.map(x=>x.id===w.id?w:x) : [...d.worlds, w]};
    });
    setModal(null);
  };
  const deleteWorld = (id) => setData(d=>({...d,worlds:d.worlds.filter(w=>w.id!==id)}));

  // Sagas
  const saveSaga = (s) => {
    setData(d => {
      const exists = d.sagas.find(x=>x.id===s.id);
      return {...d, sagas: exists ? d.sagas.map(x=>x.id===s.id?s:x) : [...d.sagas, s]};
    });
    setModal(null);
  };
  const deleteSaga = (id) => setData(d=>({...d,sagas:d.sagas.filter(s=>s.id!==id)}));

  // Families
  const saveFamily = (f) => {
    setData(d => {
      const exists = d.families.find(x=>x.id===f.id);
      return {...d, families: exists ? d.families.map(x=>x.id===f.id?f:x) : [...d.families, f]};
    });
    setModal(null);
  };
  const deleteFamily = (id) => setData(d=>({...d,families:d.families.filter(f=>f.id!==id)}));

  const tabs = [
    {id:"characters", label:"Characters", icon:"◈"},
    {id:"families", label:"Families", icon:"⌂"},
    {id:"world", label:"World", icon:"◎"},
    {id:"sagas", label:"Sagas", icon:"⚔"},
  ];

  return (
    <div style={{minHeight:"100vh",position:"relative",fontFamily:"'Cormorant Garamond',serif"}}>
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@400;600;700&family=Cormorant+Garamond:ital,wght@0,300;0,400;0,500;1,300;1,400&display=swap');
        * { box-sizing: border-box; }
        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: #010b15; }
        ::-webkit-scrollbar-thumb { background: #00e5ff33; border-radius: 3px; }
        select option { background: #021020; }
      `}</style>
      <BubbleBG/>

      {/* HEADER */}
      <div style={{position:"relative",zIndex:10,textAlign:"center",padding:"36px 20px 24px"}}>
        <div style={{fontSize:11,letterSpacing:8,color:"#00e5ff88",textTransform:"uppercase",fontFamily:"'Courier New',monospace",marginBottom:8}}>◈ ◈ ◈</div>
        <h1 style={{fontFamily:"'Cinzel',serif",fontSize:"clamp(28px,5vw,52px)",color:"#00e5ff",margin:0,letterSpacing:4,textShadow:"0 0 40px #00e5ff66, 0 0 80px #00e5ff22"}}>Writer Database</h1>
        <p style={{color:"#4499bb",fontSize:14,margin:"8px 0 0",letterSpacing:2,fontStyle:"italic"}}>Where stories are born from the deep</p>
        <div style={{width:120,height:1,background:"linear-gradient(90deg,transparent,#00e5ff44,transparent)",margin:"16px auto 0"}}/>
      </div>

      {/* TABS */}
      <div style={{position:"relative",zIndex:10,display:"flex",justifyContent:"center",gap:4,padding:"0 20px 28px",flexWrap:"wrap"}}>
        {tabs.map(t => (
          <button key={t.id} onClick={()=>setTab(t.id)} style={{
            background: tab===t.id ? "linear-gradient(135deg,#004488,#006699)" : "transparent",
            border: tab===t.id ? "1px solid #00e5ff66" : "1px solid #00e5ff22",
            borderRadius:30,
            color: tab===t.id ? "#00e5ff" : "#4499bb",
            cursor:"pointer",
            padding:"9px 22px",
            fontSize:13,
            fontFamily:"'Cinzel',serif",
            letterSpacing:2,
            transition:"all 0.2s",
            boxShadow: tab===t.id ? "0 0 20px #00e5ff22" : "none",
          }}>{t.icon} {t.label}</button>
        ))}
      </div>

      {/* CONTENT */}
      <div style={{position:"relative",zIndex:10,maxWidth:1100,margin:"0 auto",padding:"0 20px 60px"}}>

        {/* CHARACTERS TAB */}
        {tab==="characters" && (
          <div>
            <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:24}}>
              <h2 style={{color:"#7fdbff",fontFamily:"'Cinzel',serif",fontSize:18,margin:0,letterSpacing:2}}>Characters</h2>
              <Btn label="+ Create Character" onClick={()=>setModal({type:"char",payload:null})}/>
            </div>
            {data.characters.length === 0 && (
              <div style={{textAlign:"center",color:"#224455",padding:"60px 0",fontSize:16,letterSpacing:1}}>
                <div style={{fontSize:40,marginBottom:16}}>◈</div>
                No characters yet. Create your first one.
              </div>
            )}
            <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fill,minmax(280px,1fr))",gap:16}}>
              {data.characters.map(c => (
                <Card key={c.id} char={c}
                  onClick={()=>setViewing({type:"char",id:c.id})}
                  onEdit={()=>setModal({type:"char",payload:c})}
                  onDelete={()=>deleteChar(c.id)}/>
              ))}
            </div>
          </div>
        )}

        {/* FAMILIES TAB */}
        {tab==="families" && (
          <div>
            <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:24}}>
              <h2 style={{color:"#7fdbff",fontFamily:"'Cinzel',serif",fontSize:18,margin:0,letterSpacing:2}}>Family Trees</h2>
              <Btn label="+ Create Family" onClick={()=>setModal({type:"family",payload:null})}/>
            </div>
            {data.families.length === 0 && (
              <div style={{textAlign:"center",color:"#224455",padding:"60px 0",fontSize:16,letterSpacing:1}}>
                <div style={{fontSize:40,marginBottom:16}}>⌂</div>
                No families yet. Create your first family tree.
              </div>
            )}
            <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fill,minmax(280px,1fr))",gap:16}}>
              {data.families.map(f => (
                <div key={f.id} style={{background:"linear-gradient(160deg,#061828,#030f1e)",border:"1px solid #00e5ff22",borderRadius:14,padding:16,cursor:"pointer"}}
                  onMouseEnter={e=>{e.currentTarget.style.border="1px solid #00e5ff55";}}
                  onMouseLeave={e=>{e.currentTarget.style.border="1px solid #00e5ff22";}}>
                  <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start"}}>
                    <div>
                      <div style={{color:"#00e5ff",fontFamily:"'Cinzel',serif",fontSize:15,marginBottom:4}}>{f.name||"Unnamed Family"}</div>
                      <div style={{color:"#4499bb",fontSize:12}}>{f.root ? (data.characters.find(c=>c.id===f.root.charId)?.name||"Unknown root") : "No root set"}</div>
                    </div>
                    <div style={{display:"flex",gap:6}}>
                      <button onClick={()=>setViewing({type:"family",id:f.id})} style={{background:"none",border:"1px solid #00e5ff22",borderRadius:6,color:"#7fdbff",cursor:"pointer",padding:"3px 8px",fontSize:11}}>View</button>
                      <button onClick={()=>setModal({type:"family",payload:f})} style={{background:"none",border:"1px solid #00e5ff22",borderRadius:6,color:"#7fdbff",cursor:"pointer",padding:"3px 8px",fontSize:11}}>Edit</button>
                      <button onClick={()=>deleteFamily(f.id)} style={{background:"none",border:"1px solid #ff6b6b22",borderRadius:6,color:"#ff6b6b88",cursor:"pointer",padding:"3px 8px",fontSize:11}}>×</button>
                    </div>
                  </div>
                </div>
              ))}
            </div>
          </div>
        )}

        {/* WORLD TAB */}
        {tab==="world" && (
          <div>
            <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:24}}>
              <h2 style={{color:"#7fdbff",fontFamily:"'Cinzel',serif",fontSize:18,margin:0,letterSpacing:2}}>Worlds</h2>
              <Btn label="+ Create World" onClick={()=>setModal({type:"world",payload:null})}/>
            </div>
            {data.worlds.length === 0 && (
              <div style={{textAlign:"center",color:"#224455",padding:"60px 0",fontSize:16,letterSpacing:1}}>
                <div style={{fontSize:40,marginBottom:16}}>◎</div>
                No worlds yet. Build your first world.
              </div>
            )}
            <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fill,minmax(300px,1fr))",gap:16}}>
              {data.worlds.map(w => (
                <div key={w.id} style={{background:"linear-gradient(160deg,#061828,#030f1e)",border:"1px solid #00e5ff22",borderRadius:14,padding:18}}>
                  <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start",marginBottom:10}}>
                    <div style={{color:"#00e5ff",fontFamily:"'Cinzel',serif",fontSize:16}}>{w.name||"Unnamed World"}</div>
                    <div style={{display:"flex",gap:6}}>
                      <button onClick={()=>setModal({type:"world",payload:w})} style={{background:"none",border:"1px solid #00e5ff22",borderRadius:6,color:"#7fdbff",cursor:"pointer",padding:"3px 8px",fontSize:11}}>Edit</button>
                      <button onClick={()=>deleteWorld(w.id)} style={{background:"none",border:"1px solid #ff6b6b22",borderRadius:6,color:"#ff6b6b88",cursor:"pointer",padding:"3px 8px",fontSize:11}}>×</button>
                    </div>
                  </div>
                  {(w.continents||[]).map(cont => (
                    <div key={cont.id} style={{marginBottom:6}}>
                      <div style={{color:"#7fdbff",fontSize:12,fontFamily:"'Cinzel',serif"}}>🌍 {cont.name}</div>
                      {(cont.kingdoms||[]).map(k=>(
                        <div key={k.id} style={{marginLeft:12,color:"#4499bb",fontSize:11}}>
                          ⚑ {k.name} {k.cities.length>0 && <span style={{color:"#33667788"}}>· {k.cities.map(c=>c.name).join(", ")}</span>}
                        </div>
                      ))}
                    </div>
                  ))}
                  {(w.powerSystems||[]).length > 0 && (
                    <div style={{marginTop:8,borderTop:"1px solid #00e5ff11",paddingTop:8}}>
                      {(w.powerSystems||[]).map(ps=>(
                        <div key={ps.id} style={{color:"#44bbff",fontSize:11}}>✦ {ps.name} <span style={{color:"#33667788"}}>({(ps.abilities||[]).length} abilities)</span></div>
                      ))}
                    </div>
                  )}
                </div>
              ))}
            </div>
          </div>
        )}

        {/* SAGAS TAB */}
        {tab==="sagas" && (
          <div>
            <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:24}}>
              <h2 style={{color:"#7fdbff",fontFamily:"'Cinzel',serif",fontSize:18,margin:0,letterSpacing:2}}>Sagas</h2>
              <Btn label="+ Create Saga" onClick={()=>setModal({type:"saga",payload:null})}/>
            </div>
            {data.sagas.length === 0 && (
              <div style={{textAlign:"center",color:"#224455",padding:"60px 0",fontSize:16,letterSpacing:1}}>
                <div style={{fontSize:40,marginBottom:16}}>⚔</div>
                No sagas yet. Write your first epic.
              </div>
            )}
            <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fill,minmax(300px,1fr))",gap:16}}>
              {data.sagas.map(s => (
                <div key={s.id} style={{background:"linear-gradient(160deg,#061828,#030f1e)",border:"1px solid #00e5ff22",borderRadius:14,padding:18}}>
                  <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start",marginBottom:8}}>
                    <div style={{color:"#00e5ff",fontFamily:"'Cinzel',serif",fontSize:16}}>{s.name||"Unnamed Saga"}</div>
                    <div style={{display:"flex",gap:6}}>
                      <button onClick={()=>setModal({type:"saga",payload:s})} style={{background:"none",border:"1px solid #00e5ff22",borderRadius:6,color:"#7fdbff",cursor:"pointer",padding:"3px 8px",fontSize:11}}>Edit</button>
                      <button onClick={()=>deleteSaga(s.id)} style={{background:"none",border:"1px solid #ff6b6b22",borderRadius:6,color:"#ff6b6b88",cursor:"pointer",padding:"3px 8px",fontSize:11}}>×</button>
                    </div>
                  </div>
                  {s.description && <p style={{color:"#4499bb",fontSize:13,margin:"0 0 8px",lineHeight:1.5}}>{s.description}</p>}
                  {s.continent && <div style={{color:"#33667788",fontSize:11}}>🌍 {s.continent}</div>}
                  {s.powerSystem && <div style={{color:"#33667788",fontSize:11}}>✦ {s.powerSystem}</div>}
                  {(s.acts||[]).length > 0 && (
                    <div style={{marginTop:8,borderTop:"1px solid #00e5ff11",paddingTop:8}}>
                      {(s.acts||[]).map(act=>(
                        <div key={act.id} style={{color:"#7fdbff",fontSize:12,marginBottom:4}}>
                          ▸ {act.name||"Unnamed Act"}
                          {(act.villains||[]).length>0 && <span style={{color:"#ff6b6b88",marginLeft:6,fontSize:11}}>({act.villains.length} villain{act.villains.length>1?"s":""})</span>}
                        </div>
                      ))}
                    </div>
                  )}
                </div>
              ))}
            </div>
          </div>
        )}
      </div>

      {/* ---- MODALS ---- */}

      {/* CHARACTER MODAL */}
      <Modal open={modal?.type==="char"} onClose={()=>setModal(null)} title={modal?.payload ? "Edit Character" : "Create Character"} wide>
        {modal?.type==="char" && <CharacterEditor initial={modal.payload} onSave={saveChar} onCancel={()=>setModal(null)} allChars={data.characters} allWorlds={data.worlds}/>}
      </Modal>

      {/* WORLD MODAL */}
      <Modal open={modal?.type==="world"} onClose={()=>setModal(null)} title={modal?.payload ? "Edit World" : "Create World"} wide>
        {modal?.type==="world" && <WorldEditor initial={modal.payload} onSave={saveWorld} onCancel={()=>setModal(null)}/>}
      </Modal>

      {/* SAGA MODAL */}
      <Modal open={modal?.type==="saga"} onClose={()=>setModal(null)} title={modal?.payload ? "Edit Saga" : "Create Saga"} wide>
        {modal?.type==="saga" && <SagaEditor initial={modal.payload} onSave={saveSaga} onCancel={()=>setModal(null)} allChars={data.characters} allWorlds={data.worlds}/>}
      </Modal>

      {/* FAMILY MODAL */}
      <Modal open={modal?.type==="family"} onClose={()=>setModal(null)} title={modal?.payload ? "Edit Family" : "Create Family"} wide>
        {modal?.type==="family" && (
          <FamilyEditor initial={modal.payload} allChars={data.characters} onSave={saveFamily} onCancel={()=>setModal(null)}/>
        )}
      </Modal>

      {/* CHARACTER VIEW MODAL */}
      <Modal open={viewing?.type==="char"} onClose={()=>setViewing(null)} title="Character Profile" wide>
        {viewing?.type==="char" && (() => { const c = data.characters.find(x=>x.id===viewing.id); return c ? <CharacterDisplay char={c} allChars={data.characters} allWorlds={data.worlds}/> : null; })()}
      </Modal>

      {/* FAMILY VIEW MODAL */}
      <Modal open={viewing?.type==="family"} onClose={()=>setViewing(null)} title="Family Tree" wide>
        {viewing?.type==="family" && (() => {
          const f = data.families.find(x=>x.id===viewing.id);
          if (!f || !f.root) return <p style={{color:"#4499bb"}}>No root character set.</p>;
          return (
            <div style={{overflowX:"auto",padding:16}}>
              <FamilyTreeNode node={f.root} allChars={data.characters}
                onUpdate={newRoot => {
                  setData(d=>({...d,families:d.families.map(x=>x.id===f.id?{...x,root:newRoot}:x)}));
                }}/>
            </div>
          );
        })()}
      </Modal>
    </div>
  );
}

// ============================================================
// FAMILY EDITOR (name + root picker)
// ============================================================
function FamilyEditor({ initial, allChars, onSave, onCancel }) {
  const blank = { id:Date.now().toString(), name:"", root:null };
  const [f, setF] = useState(initial||blank);
  const [rootId, setRootId] = useState(initial?.root?.charId||"");

  const handleSave = () => {
    if (!rootId) { alert("Please select a root character."); return; }
    onSave({...f, root: f.root?.charId===rootId ? f.root : {charId:rootId, couples:[]}});
  };

  return (
    <div>
      <Field label="Family Name"><TextInput value={f.name} onChange={v=>setF(p=>({...p,name:v}))} placeholder="e.g. House of Ashveil..."/></Field>
      <Field label="Root Character (Family Founder)">
        <select value={rootId} onChange={e=>setRootId(e.target.value)} style={{width:"100%",background:"#021020",border:"1px solid #00e5ff33",borderRadius:8,padding:"9px 12px",color:"#cce8ff",fontSize:13,outline:"none"}}>
          <option value="">Select root character...</option>
          {allChars.map(c=><option key={c.id} value={c.id}>{c.name||"Unnamed"}</option>)}
        </select>
      </Field>
      <p style={{color:"#4499bb",fontSize:13,fontStyle:"italic"}}>After saving, open the family tree to add partners, children, and branches.</p>
      <div style={{display:"flex",gap:10,justifyContent:"flex-end",marginTop:16}}>
        <Btn label="Cancel" onClick={onCancel} small ghost/>
        <Btn label="Save Family" onClick={handleSave} small/>
      </div>
    </div>
  );
}
