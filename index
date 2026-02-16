/**
 * SIMOBE (Sistem Informasi Manajemen Outcome-Based Education)
 * * Aplikasi berbasis React untuk membantu Program Studi dalam:
 * 1. Mengelola Kurikulum (CPL, CPMK, Sub-CPMK)
 * 2. Melakukan Penilaian Mahasiswa (Input Nilai)
 * 3. Menganalisis Ketercapaian CPL secara otomatis
 * 4. Download Berkas Word (Landscape & High Fidelity)
 * 5. Backup dan Restore Data
 * * @version 1.24 (Fix Error 9 & Batch Restore Limit)
 * @license MIT
 * @author Ian Manoppo & RB Digital
 */

import React, { useState, useEffect, useMemo, useRef } from 'react';

// --- Third Party Libraries ---
import { 
  LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip as RechartsTooltip, Legend, ResponsiveContainer,
  Radar, RadarChart, PolarGrid, PolarAngleAxis, PolarRadiusAxis, PieChart, Pie, Cell
} from 'recharts';
import { 
  LayoutDashboard, BookOpen, Users, BarChart3, Settings, Plus, Save, Trash2, 
  ChevronRight, GraduationCap, ClipboardList, Calendar, UserCheck, Link2, 
  ListTodo, UserPlus, Edit2, Check, X, PlusCircle, Info, ArrowRightCircle, 
  Calculator, Trophy, Sparkles, Layers, User, Upload, Download, FileSpreadsheet, 
  FileText, AlertCircle, TrendingUp, Printer, TableProperties, UserRoundPlus, 
  ChevronDown, InfoIcon, School, Building2, MapPin, MapPinned, UserCircle, Camera,
  PieChart as PieChartIcon, CheckCircle2, FileDown, FileUp, BookCheck, Microscope, Image,
  Briefcase, Database, RefreshCw, HardDriveDownload, HardDriveUpload, FileInput,
  UserCheck2, History, ShieldAlert, Award, LogOut, FileOutput, HelpCircle
} from 'lucide-react';

// --- Firebase SDK ---
import { initializeApp } from 'firebase/app';
import { 
  getFirestore, collection, doc, setDoc, getDocs, onSnapshot, query, deleteDoc, getDoc, writeBatch
} from 'firebase/firestore';
import { 
  getAuth, signInWithCustomToken, signInAnonymously, onAuthStateChanged 
} from 'firebase/auth';

/**
 * Konfigurasi Firebase
 * Mengambil config dari env atau placeholder kosong
 */
const getFirebaseConfig = () => {
  if (typeof __firebase_config !== 'undefined' && __firebase_config) {
    return JSON.parse(__firebase_config);
  } else {
    return {
      apiKey: "",
      authDomain: "",
      projectId: "",
      storageBucket: "",
      messagingSenderId: "",
      appId: ""
    };
  }
};

const firebaseConfig = getFirebaseConfig();
const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);

const APP_ID = typeof __app_id !== 'undefined' ? __app_id : 'simobe-prodi-app';

// ==========================================
// UTILITY COMPONENTS
// ==========================================

const NavItem = ({ icon, label, active, onClick, disabled = false }) => (
  <button 
    onClick={onClick} 
    disabled={disabled}
    className={`group relative w-full flex items-center gap-4 px-6 py-4 rounded-2xl transition-all duration-300 outline-none ${
      disabled ? 'opacity-30 cursor-not-allowed grayscale' : ''
    } ${
      active 
        ? 'bg-blue-600/10 text-blue-400 font-bold shadow-[inset_0_0_20px_rgba(37,99,235,0.05)]' 
        : 'text-slate-400 hover:bg-slate-800/50 hover:text-slate-200'
    }`}
  >
    {active && (
      <div className="absolute left-0 top-1/2 -translate-y-1/2 w-1.5 h-8 bg-blue-500 rounded-r-full shadow-[0_0_15px_rgba(59,130,246,0.5)]" />
    )}
    <div className={`transition-transform duration-300 ${active ? 'scale-110' : 'group-hover:scale-110 group-hover:rotate-3'}`}>
      {icon}
    </div>
    <span className={`text-[13px] tracking-wide transition-all duration-300 ${active ? 'translate-x-1' : ''}`}>
      {label}
    </span>
    {!active && !disabled && (
      <div className="absolute right-4 opacity-0 group-hover:opacity-100 transition-all duration-300 translate-x-2 group-hover:translate-x-0">
        <ChevronRight size={14} className="text-slate-600" />
      </div>
    )}
  </button>
);

const SubNavItem = ({ id, icon, label, active, onClick, colorTheme = 'slate' }) => {
  const themeStyles = {
    blue:    { active: 'bg-blue-600 border-blue-600 shadow-blue-200',    hover: 'hover:text-blue-600 hover:border-blue-200 hover:bg-blue-50' },
    indigo:  { active: 'bg-indigo-600 border-indigo-600 shadow-indigo-200', hover: 'hover:text-indigo-600 hover:border-indigo-200 hover:bg-indigo-50' },
    emerald: { active: 'bg-emerald-600 border-emerald-600 shadow-emerald-200', hover: 'hover:text-emerald-600 hover:border-emerald-200 hover:bg-emerald-50' },
    violet:  { active: 'bg-violet-600 border-violet-600 shadow-violet-200',  hover: 'hover:text-violet-600 hover:border-violet-200 hover:bg-violet-50' },
    rose:    { active: 'bg-rose-600 border-rose-600 shadow-rose-200',    hover: 'hover:text-rose-600 hover:border-rose-200 hover:bg-rose-50' },
    slate:   { active: 'bg-slate-900 border-slate-900 shadow-slate-200',   hover: 'hover:text-slate-600 border-slate-300 hover:bg-slate-50' },
  };
  const currentTheme = themeStyles[colorTheme] || themeStyles.slate;
  return (
    <button 
      onClick={() => onClick(id)} 
      className={`flex-1 md:flex-none flex items-center justify-center md:justify-start gap-3 px-5 py-4 rounded-2xl transition-all duration-200 border whitespace-nowrap shadow-sm ${
        active 
          ? `${currentTheme.active} text-white font-black shadow-lg scale-105` 
          : `bg-white text-slate-400 border-slate-200 ${currentTheme.hover}`
      }`}
    >
      {icon} <span className="text-[10px] uppercase tracking-widest">{label}</span>
    </button>
  );
};

// ==========================================
// FEATURE COMPONENTS
// ==========================================

const DashboardPlaceholder = ({ courses, cpls, institutionData, academicSettings, profileData }) => (
  <div className="flex flex-col h-full gap-4 no-print animate-in fade-in duration-700">
    <div className="bg-gradient-to-r from-blue-600 to-indigo-700 rounded-[32px] p-6 text-white shadow-lg relative overflow-hidden flex-shrink-0">
        <div className="absolute top-0 right-0 w-64 h-64 bg-white/10 rounded-full -mr-10 -mt-10 blur-3xl"></div>
        <div className="relative z-10"><h1 className="text-2xl md:text-3xl font-black tracking-tight mb-1">Selamat Datang di SIMOBE</h1><p className="text-sm md:text-base font-bold text-blue-100 opacity-90">Sistem Manajemen Outcome-Based Education (OBE)</p></div>
    </div>
    <div className="flex-1 grid grid-cols-1 lg:grid-cols-3 gap-4 min-h-0">
      <div className="lg:col-span-2 flex flex-col gap-4 h-full">
         <div className="bg-white p-6 rounded-[32px] border shadow-sm flex flex-col sm:flex-row items-center gap-6 relative overflow-hidden group hover:shadow-md transition-all flex-shrink-0">
            <div className="relative z-10 flex-shrink-0"><div className="w-20 h-20 sm:w-24 sm:h-24 rounded-full border-4 border-white shadow-lg overflow-hidden bg-slate-100 flex items-center justify-center">{profileData?.photo ? <img src={profileData.photo} alt="User" className="w-full h-full object-cover"/> : <UserCircle size={60} className="text-slate-300"/>}</div></div>
            <div className="relative z-10 flex-1 text-center sm:text-left w-full"><h2 className="text-xl sm:text-2xl font-black text-slate-800 leading-tight mb-1">{profileData?.name || 'Nama Dosen'}</h2><p className="text-sm font-bold text-slate-500 uppercase tracking-widest flex items-center justify-center sm:justify-start gap-2"><span className="text-blue-600 font-black">NIDN/NIDK/NUPTK:</span> {profileData?.nidn || '-'}</p></div>
         </div>
         <div className="bg-white p-6 rounded-[32px] border shadow-sm flex-1 flex flex-col justify-center min-h-[220px]">
            <div className="flex items-center gap-3 mb-6"><div className="p-2 bg-indigo-50 text-indigo-600 rounded-xl"><School size={20} /></div><div><h3 className="text-lg font-black text-slate-800 uppercase tracking-tight">Identitas Institusi</h3></div></div>
            <div className="grid grid-cols-2 gap-x-6 gap-y-6">
               <div className="group"><label className="text-[9px] font-black uppercase text-slate-400 mb-1 block">Universitas</label><p className="text-sm font-black text-slate-800 truncate">{institutionData?.university || '-'}</p></div>
               <div className="group"><label className="text-[9px] font-black uppercase text-slate-400 mb-1 block">Fakultas</label><p className="text-sm font-black text-slate-800 truncate">{institutionData?.faculty || '-'}</p></div>
               <div className="group"><label className="text-[9px] font-black uppercase text-slate-400 mb-1 block">Jurusan</label><p className="text-sm font-black text-slate-800 truncate">{institutionData?.department || '-'}</p></div>
               <div className="group"><label className="text-[9px] font-black uppercase text-slate-400 mb-1 block">Program Studi</label><p className="text-sm font-black text-slate-800 truncate">{institutionData?.program || '-'}</p></div>
            </div>
         </div>
      </div>
      <div className="flex flex-col gap-4">
        <div className="bg-slate-900 text-white p-6 rounded-[32px] shadow-xl flex flex-col justify-center text-center flex-1">
           <p className="text-[9px] font-black text-slate-400 uppercase tracking-widest mb-2">Tahun Akademik</p>
           <h4 className="text-4xl font-black mb-1 leading-none">{academicSettings?.year}</h4>
           <div className="inline-block px-3 py-1 rounded-lg text-[10px] font-black uppercase bg-indigo-500 text-white mt-2">Semester {academicSettings?.semester}</div>
        </div>
        <div className="grid grid-cols-1 gap-4">
           <div className="bg-white border p-5 rounded-[24px] shadow-sm flex items-center justify-between"><div><p className="text-[9px] font-black text-slate-400 uppercase tracking-widest">CPL Prodi</p><p className="text-2xl font-black text-slate-900">{cpls?.length || 0}</p></div><div className="w-10 h-10 bg-indigo-50 text-indigo-600 rounded-xl flex items-center justify-center"><Layers size={20}/></div></div>
           <div className="bg-white border p-5 rounded-[24px] shadow-sm flex items-center justify-between"><div><p className="text-[9px] font-black text-slate-400 uppercase tracking-widest">MK Aktif</p><p className="text-2xl font-black text-slate-900">{courses?.length || 0}</p></div><div className="w-10 h-10 bg-emerald-50 text-emerald-600 rounded-xl flex items-center justify-center"><BookOpen size={20}/></div></div>
        </div>
      </div>
    </div>
  </div>
);

const CplManager = ({ cpls, onSave, onDelete }) => {
  const [form, setForm] = useState({ code: '', description: '' });
  const cplOptions = useMemo(() => Array.from({ length: 15 }, (_, i) => `CPL-${i + 1}`).filter(opt => !cpls.find(c => c.code === opt)), [cpls]);
  return (
    <div className="space-y-6">
      <div className="flex flex-col md:flex-row gap-4 items-end bg-slate-50 p-6 rounded-[32px] border">
        <div className="w-full md:w-48"><label className="text-[10px] font-black uppercase text-slate-400 block mb-2">Kode CPL</label><select value={form.code} onChange={e => setForm({...form, code: e.target.value})} className="w-full p-4 rounded-2xl border font-black outline-none bg-white"><option value="">-- CPL --</option>{cplOptions.map(opt => <option key={opt} value={opt}>{opt}</option>)}</select></div>
        <div className="flex-1 w-full"><label className="text-[10px] font-black uppercase text-slate-400 block mb-2">Deskripsi CPL</label><input value={form.description} onChange={e => setForm({...form, description: e.target.value})} placeholder="Ketik deskripsi..." className="w-full p-4 rounded-2xl border font-bold outline-none" /></div>
        <button onClick={() => {if(form.code) { onSave(form.code, form); setForm({code:'', description:''}); }}} className="w-full md:w-auto bg-blue-600 text-white px-8 py-4 rounded-2xl font-black uppercase text-xs shadow-lg flex items-center justify-center gap-2 transition-all hover:bg-blue-700"><Plus size={18}/> Tambah</button>
      </div>
      <div className="bg-white rounded-[32px] border overflow-hidden shadow-sm">
        <table className="w-full text-left font-bold border-collapse">
          <thead className="bg-slate-50 border-b">
            <tr className="text-[10px] uppercase text-slate-400 tracking-widest">
              <th className="p-5 w-32 border-r">Kode</th>
              <th className="p-5">Deskripsi</th>
              <th className="p-5 text-right">Aksi</th>
            </tr>
          </thead>
          <tbody className="divide-y divide-slate-100">
            {cpls.map(c => (
              <tr key={c.id} className="hover:bg-slate-50/50">
                <td className="p-5 font-black text-blue-600 border-r w-32">{c.code}</td>
                <td className="p-5 text-sm text-slate-600">{c.description}</td>
                <td className="p-5 text-right"><button onClick={() => onDelete(c.id)} className="text-slate-200 hover:text-rose-500 transition-colors p-2 hover:bg-rose-50 rounded-xl"><Trash2 size={20}/></button></td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>
    </div>
  );
};

const CourseManager = ({ courses, academicSettings, onSaveCourse, onDeleteCourse, onSaveAcademic }) => {
  const [form, setForm] = useState({ code: '', name: '', lecturers: [] });
  const [lecturerInput, setLecturerInput] = useState('');
  const addLecturer = () => { if (!lecturerInput.trim()) return; setForm({ ...form, lecturers: [...(form.lecturers || []), { id: Date.now().toString(), name: lecturerInput.trim() }] }); setLecturerInput(''); };
  return (
    <div className="space-y-8 animate-in fade-in">
      <div className="bg-slate-50 p-8 rounded-[40px] border grid grid-cols-1 md:grid-cols-2 gap-6 shadow-sm">
        <div><label className="text-[10px] font-black uppercase text-slate-400 block mb-2">Tahun Akademik</label><select value={academicSettings.year} onChange={e => onSaveAcademic({...academicSettings, year: e.target.value})} className="w-full p-4 bg-white border rounded-2xl font-black outline-none">{Array.from({length:6},(_,i)=>`${2024+i}/${2025+i}`).map(y => <option key={y} value={y}>{y}</option>)}</select></div>
        <div><label className="text-[10px] font-black uppercase text-slate-400 block mb-2">Semester</label><select value={academicSettings.semester} onChange={e => onSaveAcademic({...academicSettings, semester: e.target.value})} className="w-full p-4 bg-white border rounded-2xl font-black outline-none"><option value="Ganjil">Ganjil</option><option value="Genap">Genap</option></select></div>
      </div>
      <div className="bg-slate-900 p-8 rounded-[40px] text-white space-y-6 shadow-xl">
        <h4 className="font-black text-xl uppercase tracking-tight">Tambah Mata Kuliah Baru</h4>
        <div className="grid grid-cols-1 md:grid-cols-3 gap-6"><input value={form.code} onChange={e => setForm({...form, code: e.target.value})} placeholder="Kode MK..." className="p-4 bg-slate-800 border-slate-700 border rounded-2xl font-black outline-none" /><input value={form.name} onChange={e => setForm({...form, name: e.target.value})} placeholder="Nama MK..." className="md:col-span-2 p-4 bg-slate-800 border-slate-700 border rounded-2xl font-black outline-none" /></div>
        <div className="flex gap-4"><input value={lecturerInput} onChange={e => setLecturerInput(e.target.value)} placeholder="Tim Dosen..." className="flex-1 p-4 bg-slate-800 border-slate-700 border rounded-2xl font-bold outline-none" /><button onClick={addLecturer} className="bg-indigo-600 px-8 py-4 rounded-2xl font-black text-xs uppercase shadow-lg hover:bg-indigo-700">Tambah Dosen</button></div>
        <div className="flex flex-wrap gap-2">{(form.lecturers || []).map(l => <div key={l.id} className="bg-slate-800 border border-slate-700 px-4 py-2 rounded-xl flex items-center gap-3"><span className="text-xs font-bold">{l.name}</span><button onClick={() => setForm({...form, lecturers: (form.lecturers || []).filter(x => x.id !== l.id)})}><X size={14}/></button></div>)}</div>
        <button onClick={() => { if(form.code && form.name) { onSaveCourse(form.code, form); setForm({code:'', name:'', lecturers:[]}); } }} className="w-full bg-blue-600 text-white py-5 rounded-[24px] font-black uppercase shadow-xl hover:bg-blue-700 transition-all">Simpan Mata Kuliah</button>
      </div>
      <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
        {courses.map(c => (
          <div key={c.id} className="p-8 bg-white border border-slate-100 rounded-[48px] shadow-sm flex flex-col group hover:shadow-md transition-all">
            <div className="flex justify-between items-start mb-2">
              <div>
                <p className="text-[10px] font-black text-blue-600 mb-1 uppercase tracking-widest">{c.code}</p>
                <h5 className="font-black text-2xl text-slate-800 leading-tight">{c.name}</h5>
              </div>
              <button onClick={() => onDeleteCourse(c.id)} className="text-slate-200 group-hover:text-rose-500 transition-colors p-2 hover:bg-rose-50 rounded-xl"><Trash2 size={24}/></button>
            </div>
            <div className="flex flex-wrap gap-2 mt-2">
              {(c.lecturers || []).map(l => (
                <span key={l.id} className="text-[10px] bg-slate-50 text-slate-500 border border-slate-200 px-3 py-1.5 rounded-xl font-bold flex items-center gap-1.5">
                   <User size={10} className="opacity-50" /> {l.name}
                </span>
              ))}
            </div>
          </div>
        ))}
      </div>
    </div>
  );
};

const MappingManager = ({ courses, cpls, onSave }) => {
  const [selected, setSelected] = useState('');
  const course = courses.find(c => c.id === selected);
  const toggle = (code) => { 
    if (!course) return; 
    const cur = course.mappings || []; 
    const updated = cur.includes(code) ? cur.filter(x => x !== code) : [...cur, code]; 
    onSave(selected, { ...course, mappings: updated }); 
  };
  return (
    <div className="space-y-8 animate-in fade-in">
      <div className="bg-slate-900 p-8 rounded-[40px] shadow-xl"><select value={selected} onChange={e => setSelected(e.target.value)} className="w-full p-5 bg-slate-800 border-slate-700 border rounded-[24px] font-black text-white text-xl outline-none"><option value="">-- Pilih MK --</option>{courses.map(c => <option key={c.id} value={c.id}>{c.name}</option>)}</select></div>
      {selected && (<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">{cpls.map(c => (<button key={c.id} onClick={() => toggle(c.code)} className={`p-8 border-2 rounded-[40px] font-black text-left transition-all flex flex-col gap-4 ${course?.mappings?.includes(c.code) ? 'bg-blue-600 border-blue-600 text-white shadow-xl scale-[1.02]' : 'bg-white text-slate-400 border-slate-100 hover:border-blue-100'}`}><div className="flex justify-between items-center w-full"><Link2 size={24}/>{course?.mappings?.includes(c.code) && <Check size={20}/>}</div><div><p className="text-2xl">{c.code}</p><p className="text-xs mt-2 font-bold opacity-80">{(c.description || '').substring(0, 100)}...</p></div></button>))}</div>)}
    </div>
  );
};

const CpmkManager = ({ courses, onSave }) => {
  const [selectedCourseId, setSelectedCourseId] = useState('');
  const [selectedCpl, setSelectedCpl] = useState('');
  const [cpmkForm, setCpmkForm] = useState({ code: 'CPMK-1', desc: '' });
  const [subInputs, setSubInputs] = useState({});
  const course = courses.find(c => c.id === selectedCourseId);

  const allUsedSubCodes = useMemo(() => {
    if (!course?.cpmks) return [];
    return course.cpmks.flatMap(cpmk => (cpmk.subCpmks || []).map(s => s.code));
  }, [course]);

  const filteredCpmks = useMemo(() => (course?.cpmks || []).filter(c => c.cplCode === selectedCpl), [course, selectedCpl]);
  
  const addCpmk = () => { 
    if (!cpmkForm.code || !cpmkForm.desc || !course) return; 
    const updated = [...(course.cpmks || []), { id: Date.now().toString(), code: cpmkForm.code, cplCode: selectedCpl, desc: cpmkForm.desc, subCpmks: [] }]; 
    onSave(selectedCourseId, { ...course, cpmks: updated }); 
    setCpmkForm({ code: 'CPMK-1', desc: '' }); 
  };
  
  const addSubCpmk = (cpmkId) => { 
    const currentInput = subInputs[cpmkId] || { code: '', desc: '' };
    if (!currentInput.desc || !course || !currentInput.code) return; 
    const newSub = { id: Date.now().toString(), code: currentInput.code, desc: currentInput.desc };
    const updatedCpmks = (course.cpmks || []).map(c => c.id === cpmkId ? { ...c, subCpmks: [...(c.subCpmks || []), newSub] } : c);
    onSave(selectedCourseId, { ...course, cpmks: updatedCpmks }); 
    setSubInputs(prev => ({ ...prev, [cpmkId]: { code: '', desc: '' } })); 
  };
  
  return (
    <div className="space-y-8 animate-in fade-in">
      <div className="bg-slate-900 p-8 rounded-[40px] shadow-xl"><select value={selectedCourseId} onChange={e => { setSelectedCourseId(e.target.value); setSelectedCpl(''); }} className="w-full p-5 bg-slate-800 border-slate-700 border rounded-[24px] font-black text-white text-xl outline-none focus:border-blue-500"><option value="">-- Pilih Mata Kuliah --</option>{courses.map(c => <option key={c.id} value={c.id}>{c.name}</option>)}</select></div>
      {selectedCourseId && (
        <div className="space-y-8">
          <div className="flex flex-wrap gap-3">{(course?.mappings || []).map(code => (<button key={code} onClick={() => setSelectedCpl(code)} className={`px-6 py-4 rounded-2xl text-sm font-black transition-all ${selectedCpl === code ? 'bg-blue-600 text-white shadow-xl scale-105' : 'bg-slate-50 text-slate-400'}`}>{code}</button>))}</div>
          {selectedCpl && (
            <div className="space-y-10">
              <div className="bg-slate-50 p-8 rounded-[40px] border-2 border-dashed border-slate-200 shadow-sm"><div className="grid grid-cols-1 md:grid-cols-4 gap-6 items-end"><div><label className="text-[9px] font-black text-slate-400 uppercase block mb-2">Kode CPMK</label><select value={cpmkForm.code} onChange={e => setCpmkForm({...cpmkForm, code: e.target.value})} className="w-full p-4 bg-white border rounded-2xl font-black outline-none">{Array.from({length:15},(_,i)=>`CPMK-${i+1}`).map(opt => <option key={opt} value={opt}>{opt}</option>)}</select></div><div className="md:col-span-2"><label className="text-[9px] font-black text-slate-400 uppercase block mb-2">Deskripsi CPMK</label><input value={cpmkForm.desc} onChange={e => setCpmkForm({...cpmkForm, desc: e.target.value})} className="w-full p-4 bg-white border rounded-2xl font-bold outline-none" placeholder="Tujuan capaian..." /></div><button onClick={addCpmk} className="bg-slate-900 text-white px-8 py-4 rounded-2xl font-black uppercase text-xs h-[58px] hover:bg-black transition-all">Simpan CPMK</button></div></div>
              <div className="grid grid-cols-1 lg:grid-cols-2 gap-8">
                {filteredCpmks.map(cpmk => {
                  const availableSubCodes = Array.from({length:20},(_,i)=>`Sub CPMK-${i+1}`).filter(opt => !allUsedSubCodes.includes(opt));
                  return (
                    <div key={cpmk.id} className="bg-white border-2 border-slate-100 rounded-[48px] overflow-hidden shadow-sm flex flex-col hover:shadow-md transition-all">
                      <div className="p-8 bg-slate-50 border-b flex justify-between items-start">
                        <div><span className="bg-indigo-600 text-white px-4 py-1.5 rounded-full text-[10px] font-black uppercase tracking-widest">{cpmk.code}</span><p className="font-bold text-slate-800 text-lg mt-3">{cpmk.desc}</p></div>
                        <button onClick={() => onSave(selectedCourseId, {...course, cpmks: (course.cpmks || []).filter(x => x.id !== cpmk.id)})} className="text-rose-500 hover:bg-rose-50 p-2 rounded-xl"><Trash2 size={24}/></button>
                      </div>
                      <div className="p-8 space-y-3 flex-1">
                        {(cpmk.subCpmks || []).map(sub => (
                          <div key={sub.id} className="p-4 bg-slate-50 rounded-2xl border flex justify-between items-center group">
                            <div><span className="font-black text-blue-600 text-[10px] uppercase tracking-widest">{sub.code}</span><p className="text-xs font-bold text-slate-600">{sub.desc}</p></div>
                            <button onClick={() => onSave(selectedCourseId, {...course, cpmks: (course.cpmks || []).map(c => c.id === cpmk.id ? {...c, subCpmks: (c.subCpmks || []).filter(s => s.id !== sub.id)} : c)})} className="text-slate-300 group-hover:text-rose-500 transition-colors"><Trash2 size={16}/></button>
                          </div>
                        ))}
                        {(cpmk.subCpmks || []).length === 0 && <p className="text-center text-[10px] text-slate-300 uppercase py-4 font-black tracking-widest">Belum ada Sub-CPMK</p>}
                      </div>
                      <div className="p-8 bg-slate-50/50 border-t flex gap-3">
                        <select value={subInputs[cpmk.id]?.code || ''} onChange={e => setSubInputs(prev => ({...prev, [cpmk.id]: { ...(prev[cpmk.id] || {}), code: e.target.value }}))} className="w-36 p-3 bg-white border rounded-xl font-black text-xs outline-none focus:border-blue-500">
                          <option value="">-- Sub --</option>
                          {availableSubCodes.map(opt => <option key={opt} value={opt}>{opt}</option>)}
                        </select>
                        <input value={subInputs[cpmk.id]?.desc || ''} onChange={e => setSubInputs(prev => ({...prev, [cpmk.id]: { ...(prev[cpmk.id] || {}), desc: e.target.value }}))} placeholder="Deskripsi sub..." className="flex-1 p-3 bg-white border rounded-xl font-bold text-xs outline-none focus:border-blue-500" />
                        <button onClick={() => addSubCpmk(cpmk.id)} disabled={!subInputs[cpmk.id]?.code} className="bg-slate-900 text-white p-3 rounded-xl hover:bg-black transition-all disabled:opacity-30"><Plus size={18}/></button>
                      </div>
                    </div>
                  );
                })}
              </div>
            </div>
          )}
        </div>
      )}
    </div>
  );
};

const GradingSystem = ({ courses, onSaveConfig, gradingConfigs, activeModel, setActiveModel }) => {
  const [selectedCourseId, setSelectedCourseId] = useState('');
  const [components, setComponents] = useState([]);
  const [form, setForm] = useState({ 
    meeting: '1', 
    cplCode: '', 
    cpmkId: '', 
    subCpmkId: '', 
    assessmentType: '', 
    weight: '' 
  });

  const course = useMemo(() => courses.find(c => c.id === selectedCourseId), [courses, selectedCourseId]);
  
  const mappedCpls = useMemo(() => course?.mappings || [], [course]);
  
  const courseCpmks = useMemo(() => {
    if (!course?.cpmks) return [];
    return course.cpmks.filter(c => c.cplCode === form.cplCode);
  }, [course, form.cplCode]);
  
  const courseSubCpmks = useMemo(() => {
    const selectedCpmk = courseCpmks.find(c => c.id === form.cpmkId);
    return selectedCpmk?.subCpmks || [];
  }, [courseCpmks, form.cpmkId]);

  const assessmentOptions = useMemo(() => [
    "UTS", "UTS 1", "UTS 2", "UTS 3", "UTS 4", "UTS 5",
    "UAS", "UAS 1", "UAS 2", "UAS 3", "UAS 4", "UAS 5",
    "Kuis 1", "Kuis 2", "Kuis 3", "Kuis 4", "Kuis 5",
    "Partisipasi", "Kehadiran",
    "Tugas 1", "Tugas 2", "Tugas 3", "Tugas 4", "Tugas 5", "Tugas 6", "Tugas 7",
    "Tugas Kelompok 1", "Tugas Kelompok 2", "Tugas Kelompok 3",
    "Praktikum",
    "Project 1", "Project 2", "Project 3", "Project 4", "Project 5"
  ], []);
  
  useEffect(() => { 
    const saved = gradingConfigs.find(c => c.courseId === selectedCourseId && c.modelType === activeModel); 
    setComponents(saved?.components || []); 
  }, [selectedCourseId, activeModel, gradingConfigs]);
  
  const addComponent = () => {
    if (!form.subCpmkId || !form.assessmentType || !form.weight) return;
    const sub = courseSubCpmks.find(s => s.id === form.subCpmkId);
    const cpmk = courseCpmks.find(c => c.id === form.cpmkId);
    
    setComponents([...components, { 
      id: Date.now().toString(), 
      meeting: form.meeting, 
      cplCode: form.cplCode, 
      cpmkId: form.cpmkId,
      cpmkCode: cpmk?.code, 
      subCpmkId: form.subCpmkId, 
      subCode: sub?.code, 
      name: form.assessmentType, 
      weight: Number(form.weight) 
    }]);
    
    setForm({ ...form, weight: '' });
  };
  
  const targetWeight = activeModel === '1' ? 100 : 85;
  const currentTotal = components.reduce((acc, curr) => acc + Number(curr.weight), 0);
  const isIdeal = currentTotal === targetWeight;
  
  return (
    <div className="space-y-8 animate-in fade-in duration-500">
      <div className="bg-slate-900 p-8 rounded-[40px] text-white flex flex-col md:flex-row gap-6 items-end justify-between shadow-xl">
        <div className="flex bg-slate-800 p-1 rounded-2xl w-fit">
            <button onClick={() => setActiveModel('1')} className={`px-8 py-3 rounded-xl text-xs font-black transition-all ${activeModel === '1' ? 'bg-blue-600 text-white shadow-lg' : 'text-slate-500'}`}>MODEL 1 (100%)</button>
            <button onClick={() => setActiveModel('2')} className={`px-8 py-3 rounded-xl text-xs font-black transition-all ${activeModel === '2' ? 'bg-blue-600 text-white shadow-lg' : 'text-slate-500'}`}>MODEL 2 (85%)</button>
        </div>
        <div className="flex-1 w-full md:max-w-md">
            <label className="text-[10px] font-black uppercase text-slate-500 mb-2 block tracking-widest leading-none">Pilih Mata Kuliah</label>
            <select value={selectedCourseId} onChange={e => setSelectedCourseId(e.target.value)} className="w-full p-4 bg-slate-800 border-slate-700 border rounded-2xl font-black text-white text-xl outline-none focus:border-blue-500">
                <option value="">-- Pilih MK --</option>
                {courses.map(c => <option key={c.id} value={c.id}>{c.name}</option>)}
            </select>
        </div>
      </div>

      {selectedCourseId && (
        <div className="space-y-8">
          <div className="bg-white p-10 rounded-[40px] border shadow-sm flex flex-col md:flex-row gap-8 items-center animate-in slide-in-from-top-4 duration-500">
            <div className="flex-1 space-y-8 w-full">
                <div className="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-6 gap-4 items-end">
                    <div>
                      <label className="text-[10px] font-black uppercase text-slate-400 mb-2 block">Pertemuan</label>
                      <select value={form.meeting} onChange={e => setForm({...form, meeting: e.target.value})} className="w-full p-4 bg-slate-50 border rounded-2xl font-bold outline-none focus:ring-2 focus:ring-blue-500">
                        {Array.from({length:16}, (_,i) => i+1).map(m => <option key={m} value={m}>Ke-{m}</option>)}
                      </select>
                    </div>
                    <div>
                      <label className="text-[10px] font-black uppercase text-slate-400 mb-2 block">CPL</label>
                      <select value={form.cplCode} onChange={e => setForm({...form, cplCode: e.target.value, cpmkId: '', subCpmkId: ''})} className="w-full p-4 bg-slate-50 border rounded-2xl font-bold outline-none focus:ring-2 focus:ring-blue-500">
                        <option value="">-- CPL --</option>
                        {mappedCpls.map(c => <option key={c} value={c}>{c}</option>)}
                      </select>
                    </div>
                    <div>
                      <label className="text-[10px] font-black uppercase text-slate-400 mb-2 block">CPMK</label>
                      <select value={form.cpmkId} onChange={e => setForm({...form, cpmkId: e.target.value, subCpmkId: ''})} className="w-full p-4 bg-slate-50 border rounded-2xl font-bold outline-none focus:ring-2 focus:ring-blue-500" disabled={!form.cplCode}>
                        <option value="">-- CPMK --</option>
                        {courseCpmks.map(c => <option key={c.id} value={c.id}>{c.code}</option>)}
                      </select>
                    </div>
                    <div>
                      <label className="text-[10px] font-black uppercase text-slate-400 mb-2 block">Sub CPMK</label>
                      <select value={form.subCpmkId} onChange={e => setForm({...form, subCpmkId: e.target.value})} className="w-full p-4 bg-slate-50 border rounded-2xl font-bold outline-none focus:ring-2 focus:ring-blue-500" disabled={!form.cpmkId}>
                        <option value="">-- Sub --</option>
                        {courseSubCpmks.map(s => <option key={s.id} value={s.id}>{s.code}</option>)}
                      </select>
                    </div>
                    <div>
                      <label className="text-[10px] font-black uppercase text-slate-400 mb-2 block">Bentuk Penilaian</label>
                      <select value={form.assessmentType} onChange={e => setForm({...form, assessmentType: e.target.value})} className="w-full p-4 bg-slate-50 border rounded-2xl font-bold outline-none focus:ring-2 focus:ring-blue-500">
                        <option value="">-- Pilih --</option> 
                        {assessmentOptions.map(t => <option key={t} value={t}>{t}</option>)}
                      </select>
                    </div>
                    <div>
                      <label className="text-[10px] font-black uppercase text-slate-400 mb-2 block">Bobot %</label>
                      <div className="flex gap-2">
                        <input type="number" value={form.weight} onChange={e => setForm({...form, weight: e.target.value})} className="w-full p-4 bg-slate-50 border rounded-2xl font-black text-center outline-none focus:ring-2 focus:ring-blue-500" placeholder="0" />
                        <button onClick={addComponent} className="bg-slate-900 text-white p-4 rounded-2xl hover:bg-black transition-all hover:scale-105 active:scale-95 shadow-md"><Plus/></button>
                      </div>
                    </div>
                </div>
                <button onClick={() => onSaveConfig(`${selectedCourseId}-${activeModel}`, {courseId: selectedCourseId, modelType: activeModel, components})} className="w-full bg-blue-600 text-white py-4 rounded-2xl font-black uppercase text-xs shadow-lg hover:bg-blue-700 transition-all flex items-center justify-center gap-2 tracking-widest"><Save size={16}/> Simpan Konfigurasi Mata Kuliah</button>
            </div>
            <div className="flex flex-col items-center flex-shrink-0">
                <div className="w-40 h-40 relative">
                    <ResponsiveContainer width="100%" height="100%">
                        <PieChart>
                            <Pie 
                              data={[{ name: 'Terisi', value: Math.min(currentTotal, targetWeight) }, { name: 'Sisa', value: Math.max(0, targetWeight - currentTotal) }]} 
                              innerRadius={45} 
                              outerRadius={65} 
                              paddingAngle={5} 
                              dataKey="value" 
                              startAngle={90} 
                              endAngle={-270}
                            >
                                <Cell fill={isIdeal ? '#10b981' : (currentTotal > targetWeight ? '#f43f5e' : '#3b82f6')} stroke="none" />
                                <Cell fill="#f1f5f9" stroke="none" />
                            </Pie>
                        </PieChart>
                    </ResponsiveContainer>
                    <div className="absolute inset-0 flex items-center justify-center flex-col">
                        <span className={`text-2xl font-black ${isIdeal ? 'text-emerald-600' : (currentTotal > targetWeight ? 'text-rose-600' : 'text-slate-800')}`}>{currentTotal}%</span>
                        <span className="text-[9px] font-black text-slate-400 uppercase tracking-tighter">Target: {targetWeight}%</span>
                    </div>
                </div>
                {isIdeal && (
                  <div className="flex items-center gap-1.5 text-emerald-600 bg-emerald-50 px-3 py-1 rounded-full animate-bounce">
                    <CheckCircle2 size={12}/><span className="text-[9px] font-black uppercase tracking-widest">Bobot Ideal</span>
                  </div>
                )}
            </div>
          </div>
          <div className="bg-white rounded-[40px] border overflow-hidden shadow-sm animate-in fade-in slide-in-from-bottom-4 duration-700">
            <table className="w-full text-left text-sm border-collapse">
                <thead className="bg-slate-900 text-white uppercase font-black text-[10px] tracking-widest">
                    <tr>
                      <th className="p-5 border-r border-slate-800 text-center">No</th>
                      <th className="p-5 border-r border-slate-800">Pertemuan</th>
                      <th className="p-5 border-r border-slate-800">Kode CPL</th>
                      <th className="p-5 border-r border-slate-800">Kode CPMK</th>
                      <th className="p-5 border-r border-slate-800">Kode Sub CPMK</th>
                      <th className="p-5 border-r border-slate-800">Bentuk Penilaian</th>
                      <th className="p-5 text-center border-r border-slate-800">Bobot (%)</th>
                      <th className="p-5 text-center">Aksi</th>
                    </tr>
                </thead>
                <tbody className="divide-y divide-slate-100 font-bold text-slate-700">
                    {components.sort((a,b) => Number(a.meeting) - Number(b.meeting)).map((c, idx) => (
                        <tr key={c.id} className="hover:bg-slate-50 transition-colors group">
                            <td className="p-5 text-center bg-slate-50/50 text-slate-400 text-xs border-r border-slate-100">{idx + 1}</td>
                            <td className="p-5 border-r border-slate-100">Pertemuan {c.meeting}</td>
                            <td className="p-5 border-r border-slate-100 text-indigo-600 font-black">{c.cplCode}</td>
                            <td className="p-5 border-r border-slate-100 text-blue-600 font-black">{c.cpmkCode}</td>
                            <td className="p-5 border-r border-slate-100 text-emerald-600 font-black">{c.subCode}</td>
                            <td className="p-5 border-r border-slate-100">{c.name}</td>
                            <td className="p-5 text-center border-r border-slate-100 bg-blue-50/30 text-blue-700 font-black">{c.weight}%</td>
                            <td className="p-5 text-center">
                              <button onClick={() => setComponents(components.filter(x => x.id !== c.id))} className="text-slate-300 hover:text-rose-500 transition-all p-2 hover:bg-rose-50 rounded-xl">
                                <Trash2 size={20}/>
                              </button>
                            </td>
                        </tr>
                    ))}
                    {components.length === 0 && (
                      <tr><td colSpan="8" className="p-24 text-center text-slate-300 italic">Belum ada komponen penilaian. Tambahkan melalui form di atas.</td></tr>
                    )}
                </tbody>
                <tfoot className="bg-slate-900 text-white font-black text-xs">
                  <tr>
                    <td colSpan="6" className="p-5 text-right uppercase tracking-[0.2em] text-[10px]">Total Akumulasi Bobot Penilaian:</td>
                    <td className={`p-5 text-center text-xl border-l border-slate-800 ${isIdeal ? 'bg-emerald-600' : (currentTotal > targetWeight ? 'bg-rose-600' : 'bg-blue-700')}`}>
                      {currentTotal}%
                    </td>
                    <td className="p-5 bg-slate-800 text-center">
                      {isIdeal ? <Check size={24}/> : (currentTotal > targetWeight ? <AlertCircle size={24}/> : <Info size={24}/>)}
                    </td>
                  </tr>
                </tfoot>
            </table>
          </div>
        </div>
      )}
    </div>
  );
};

const InputGrades = ({ courses, gradingConfigs, studentGrades, onSaveStudent, onDeleteStudent, activeModel, setActiveModel }) => {
  const [selectedCourseId, setSelectedCourseId] = useState('');
  const fileInputRef = useRef(null);
  const currentConfig = useMemo(() => gradingConfigs.find(c => c.courseId === selectedCourseId && c.modelType === activeModel), [gradingConfigs, selectedCourseId, activeModel]);
  const components = useMemo(() => (currentConfig?.components || []).sort((a,b) => Number(a.meeting) - Number(b.meeting)), [currentConfig]);
  const filteredStudents = useMemo(() => studentGrades.filter(s => s.courseId === selectedCourseId && s.model === activeModel), [studentGrades, selectedCourseId, activeModel]);
  
  const handleImport = (e) => {
    const file = e.target.files[0]; if (!file) return;
    const reader = new FileReader();
    reader.onload = (evt) => {
      const rows = evt.target.result.split("\n");
      for (let i = 1; i < rows.length; i++) {
        const cols = rows[i].split(","); if (cols.length < 2 || !cols[0]) continue;
        const scores = {}; let colIdx = 2;
        if (activeModel === '2') { scores['fixed_participation'] = Number(cols[colIdx]) || 0; colIdx++; }
        components.forEach(comp => { scores[comp.id] = Number(cols[colIdx]) || 0; colIdx++; });
        onSaveStudent(Date.now().toString() + i, { id: Date.now().toString() + i, courseId: selectedCourseId, model: activeModel, name: cols[0].trim(), nim: cols[1].trim(), scores: scores });
      }
    };
    reader.readAsText(file);
  };

  const handleDownloadTemplate = () => {
    if (!selectedCourseId || components.length === 0) return;
    let csvContent = "Nama Mahasiswa,NIM";
    if (activeModel === '2') csvContent += ",Partisipasi (Sikap)";
    components.forEach(comp => { csvContent += `,${comp.name} (${comp.subCode})`; });
    csvContent += "\nContoh Mahasiswa,20261001";
    if (activeModel === '2') csvContent += ",85";
    components.forEach(() => { csvContent += ",80"; });
    const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
    const url = URL.createObjectURL(blob);
    const link = document.createElement("a");
    link.setAttribute("href", url);
    link.setAttribute("download", `Template_Nilai_${activeModel === '1' ? 'M1' : 'M2'}.csv`);
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  };
  
  return (
    <div className="space-y-8 animate-in fade-in">
       <div className="bg-slate-900 p-8 rounded-[40px] text-white flex flex-col md:flex-row gap-6 items-end justify-between shadow-xl">
          <div className="flex bg-slate-800 p-1 rounded-2xl">
            <button onClick={() => setActiveModel('1')} className={`px-8 py-3 rounded-xl text-xs font-black transition-all ${activeModel === '1' ? 'bg-blue-600 text-white shadow-lg' : 'text-slate-500'}`}>MODEL 1</button>
            <button onClick={() => setActiveModel('2')} className={`px-8 py-3 rounded-xl text-xs font-black transition-all ${activeModel === '2' ? 'bg-blue-600 text-white shadow-lg' : 'text-slate-500'}`}>MODEL 2</button>
          </div>
          <div className="flex-1 w-full md:max-w-md"><label className="text-[10px] font-black uppercase text-slate-500 mb-2 block tracking-widest leading-none">Pilih Mata Kuliah</label><select value={selectedCourseId} onChange={e => setSelectedCourseId(e.target.value)} className="w-full p-4 bg-slate-800 border border-slate-700 rounded-2xl font-bold outline-none text-white focus:border-blue-500"><option value="">-- Pilih MK --</option>{courses.map(c => <option key={c.id} value={c.id}>{c.code} - {c.name}</option>)}</select></div>
       </div>
       {selectedCourseId && currentConfig ? (
         <div className="space-y-6">
           <div className="flex flex-wrap items-center justify-between gap-4">
              <div className="flex flex-wrap gap-3">
                <button onClick={() => onSaveStudent(Date.now().toString(), {id: Date.now().toString(), courseId: selectedCourseId, model: activeModel, name: '', nim: '', scores: {}})} className="bg-blue-600 text-white px-6 py-4 rounded-2xl font-black uppercase text-[10px] flex items-center gap-2 shadow-lg hover:bg-blue-700 transition-all"><UserPlus size={18}/> Tambah Mahasiswa</button>
                <button onClick={handleDownloadTemplate} className="bg-emerald-600 text-white px-6 py-4 rounded-2xl font-black uppercase text-[10px] flex items-center gap-2 shadow-lg hover:bg-emerald-700 transition-all"><FileSpreadsheet size={18}/> Unduh Template CSV</button>
              </div>
              <div className="flex gap-3 no-print">
                <button onClick={() => fileInputRef.current?.click()} className="bg-slate-900 text-white px-6 py-4 rounded-2xl font-black uppercase text-[10px] flex items-center gap-2 shadow-lg hover:bg-black transition-all"><Upload size={18}/> Import Nilai (.csv)</button>
                <input type="file" ref={fileInputRef} onChange={handleImport} className="hidden" accept=".csv" />
              </div>
           </div>
           <div className="bg-white rounded-[40px] border border-slate-100 overflow-hidden shadow-sm overflow-x-auto"><table className="w-full text-left text-xs border-collapse"><thead className="bg-slate-900 text-white uppercase font-black border-b border-slate-800"><tr><th className="p-5 w-16 text-center border-r border-slate-800">No</th><th className="p-5 min-w-[250px] border-r border-slate-800">Nama Mahasiswa</th><th className="p-5 w-40 border-r border-slate-800 text-center">NIM</th>{activeModel === '2' && (<th className="p-5 text-center bg-emerald-600/20 text-emerald-400 w-28 border-r border-slate-800">Partisipasi<br/><span className="text-[9px] opacity-70">Fixed 15%</span></th>)}{components.map(c => (<th key={c.id} className="p-5 text-center min-w-[110px] border-r border-slate-800">{c.name}<br/><span className="text-[9px] text-slate-400 font-bold tracking-widest">{c.weight}%</span></th>))}<th className="p-5 w-16 text-center">Aksi</th></tr></thead><tbody className="divide-y divide-slate-100 font-bold text-slate-700">{filteredStudents.map((s, idx) => (<tr key={s.id} className="hover:bg-slate-50 transition-colors"><td className="p-5 text-center text-slate-400 bg-slate-50/50">{idx + 1}</td><td className="p-5"><input value={s.name} onChange={e => onSaveStudent(s.id, {...s, name: e.target.value})} className="w-full bg-transparent outline-none focus:text-blue-600" placeholder="Ketik Nama..." /></td><td className="p-5 text-center"><input value={s.nim} onChange={e => onSaveStudent(s.id, {...s, nim: e.target.value})} className="w-full text-center bg-slate-50 p-2 rounded-xl border border-transparent focus:border-blue-200 outline-none" placeholder="NIM..." /></td>{activeModel === '2' && (<td className="p-5 bg-emerald-50/30 text-center"><input type="number" value={s.scores?.['fixed_participation'] || ''} onChange={e => onSaveStudent(s.id, {...s, scores: {...s.scores, 'fixed_participation': Number(e.target.value)}})} className="w-20 mx-auto block text-center p-2 bg-white border border-emerald-100 rounded-xl font-black text-emerald-700 outline-none" placeholder="0"/></td>)}{components.map(c => (<td key={c.id} className="p-5 border-r border-slate-50"><input type="number" value={s.scores?.[c.id] || ''} onChange={e => onSaveStudent(s.id, {...s, scores: {...s.scores, [c.id]: Number(e.target.value)}})} className="w-16 mx-auto block text-center p-2 bg-white border border-slate-200 rounded-xl focus:border-blue-500 outline-none transition-all font-black" placeholder="0"/></td>))}<td className="p-5 text-center"><button onClick={() => onDeleteStudent(s.id)} className="text-slate-300 hover:text-rose-500 transition-colors p-2 hover:bg-rose-50 rounded-xl"><Trash2 size={18}/></button></td></tr>))}</tbody></table></div>
         </div>
       ) : <div className="py-40 text-center text-slate-300 font-black uppercase text-sm italic">Pilih MK untuk Menginput Nilai</div>}
    </div>
  );
};

const PortfolioManager = ({ courses, gradingConfigs, studentGrades, activeModel, setActiveModel, embedView = false, externalCourseId = '', externalStudentId = '' }) => {
  const [internalCourseId, setInternalCourseId] = useState('');
  const [internalStudentId, setInternalStudentId] = useState('');
  const courseId = embedView ? externalCourseId : internalCourseId;
  const studentId = embedView ? externalStudentId : internalStudentId;
  const course = useMemo(() => courses.find(c => c.id === courseId), [courses, courseId]);
  const currentConfig = useMemo(() => gradingConfigs.find(c => c.courseId === courseId && c.modelType === activeModel), [gradingConfigs, courseId, activeModel]);
  const student = useMemo(() => studentGrades.find(s => s.id === studentId && s.courseId === courseId && s.model === activeModel), [studentGrades, studentId, courseId, activeModel]);
  const availableStudents = useMemo(() => studentGrades.filter(s => s.courseId === courseId && s.model === activeModel), [studentGrades, courseId, activeModel]);

  const tableData = useMemo(() => {
    if (!currentConfig?.components || !student) return [];
    return [...currentConfig.components]
      .sort((a, b) => Number(a.meeting) - Number(b.meeting))
      .map(comp => {
        const score = student.scores?.[comp.id] || 0;
        const weight = Number(comp.weight) || 0;
        const weightedScoreValue = score * (weight / 100);
        const achievementValue = weight > 0 ? (weightedScoreValue / weight) * 100 : 0;
        return { ...comp, score, weightedScore: weightedScoreValue, achievement: achievementValue, status: score < 55 ? 'REMEDIAL' : 'TUNTAS' };
      });
  }, [currentConfig, student]);

  const totalWeightValue = useMemo(() => {
    const componentTotal = tableData.reduce((acc, curr) => acc + curr.weight, 0);
    return activeModel === '2' ? componentTotal + 15 : componentTotal;
  }, [tableData, activeModel]);

  const finalScoreValue = useMemo(() => {
    const componentWeightedTotal = tableData.reduce((acc, curr) => acc + curr.weightedScore, 0);
    if (activeModel === '2') {
      const participationScore = student?.scores?.['fixed_participation'] || 0;
      const participationWeighted = participationScore * (15 / 100);
      return componentWeightedTotal + participationWeighted;
    }
    return componentWeightedTotal;
  }, [tableData, student, activeModel]);

  return (
    <div className="space-y-8 animate-in fade-in">
      {!embedView && (
        <div className="bg-slate-900 p-8 rounded-[40px] text-white shadow-xl no-print">
           <div className="flex flex-col md:flex-row gap-6 items-end justify-between">
              <div className="flex bg-slate-800 p-1 rounded-2xl w-fit">
                 <button onClick={() => setActiveModel('1')} className={`px-8 py-3 rounded-xl text-xs font-black transition-all ${activeModel === '1' ? 'bg-blue-600 text-white' : 'text-slate-500'}`}>MODEL 1</button>
                 <button onClick={() => setActiveModel('2')} className={`px-8 py-3 rounded-xl text-xs font-black transition-all ${activeModel === '2' ? 'bg-blue-600 text-white' : 'text-slate-500'}`}>MODEL 2</button>
              </div>
              <div className="flex-1 w-full grid grid-cols-1 md:grid-cols-2 gap-4">
                 <div><label className="text-[10px] font-black uppercase text-slate-500 mb-2 block tracking-widest leading-none">Pilih Mata Kuliah</label><select value={internalCourseId} onChange={e => { setInternalCourseId(e.target.value); setInternalStudentId(''); }} className="w-full p-4 bg-slate-800 border border-slate-700 rounded-2xl font-bold"><option value="">-- Pilih MK --</option>{courses.map(c => <option key={c.id} value={c.id}>{c.name}</option>)}</select></div>
                 <div><label className="text-[10px] font-black uppercase text-slate-500 mb-2 block tracking-widest leading-none">Pilih Mahasiswa</label><select value={internalStudentId} onChange={e => setInternalStudentId(e.target.value)} className="w-full p-4 bg-slate-800 border border-slate-700 rounded-2xl font-bold"><option value="">-- Pilih Mahasiswa --</option>{availableStudents.map(s => <option key={s.id} value={s.id}>{s.name} ({s.nim})</option>)}</select></div>
              </div>
           </div>
        </div>
      )}
      <div id="report-content" className="space-y-6">
        {courseId && studentId && student ? (
          <div className="space-y-6">
            <div className="bg-white p-8 rounded-[40px] border shadow-sm text-center">
              <h2 className="font-black text-2xl uppercase">Portofolio Penilaian Mata Kuliah {course?.name}</h2>
              <div className="flex items-center justify-center gap-2 mt-2">
                <span className="px-4 py-1 bg-blue-50 text-blue-600 rounded-full font-black text-xs uppercase">Mahasiswa: {student.name}</span>
                <span className="px-4 py-1 bg-slate-100 text-slate-500 rounded-full font-black text-xs uppercase">NIM: {student.nim}</span>
              </div>
            </div>
            <div className="bg-white rounded-[40px] border overflow-hidden shadow-sm overflow-x-auto">
              <table className="w-full text-left text-[10px] border-collapse" border="1" cellPadding="10">
                <thead className="bg-slate-900 text-white uppercase font-black">
                  <tr>
                    <th className="p-4 text-center border-r border-slate-800">Ptm</th>
                    <th className="p-4 border-r border-slate-800">CPL</th>
                    <th className="p-4 border-r border-slate-800">CPMK</th>
                    <th className="p-4 border-r border-slate-800">Sub CPMK</th>
                    <th className="p-4 border-r border-slate-800">Bentuk Penilaian</th>
                    <th className="p-4 text-center border-r border-slate-800">Bobot (%)</th>
                    <th className="p-4 text-center border-r border-slate-800">Nilai</th>
                    <th className="p-4 text-center border-r border-slate-800 bg-blue-800">Nilai Terbobot</th>
                    <th className="p-4 text-center border-r border-slate-800 bg-indigo-800">Ketercapaian (%)</th>
                    <th className="p-4 text-center">Perbaikan</th>
                  </tr>
                </thead>
                <tbody className="divide-y divide-slate-100 font-bold text-slate-700">
                  {activeModel === '2' && (
                    <tr className="bg-emerald-50/30">
                      <td className="p-4 text-center">1-16</td>
                      <td className="p-4 text-center">-</td>
                      <td className="p-4 text-center">-</td>
                      <td className="p-4 text-center">Sikap</td>
                      <td className="p-4">Partisipasi (Fixed)</td>
                      <td className="p-4 text-center">15%</td>
                      <td className="p-4 text-center">{student.scores?.['fixed_participation'] || 0}</td>
                      <td className="p-4 text-center bg-blue-50 text-blue-700">{((student.scores?.['fixed_participation'] || 0) * 0.15).toFixed(2)}</td>
                      <td className="p-4 text-center bg-indigo-50 text-indigo-700">{(student.scores?.['fixed_participation'] || 0).toFixed(1)}%</td>
                      <td className="p-4 text-center">{(student.scores?.['fixed_participation'] || 0) < 55 ? <span className="text-rose-600 font-bold">REMEDIAL</span> : 'TUNTAS'}</td>
                    </tr>
                  )}
                  {tableData.map((row, idx) => (
                    <tr key={idx} className="hover:bg-slate-50">
                      <td className="p-4 text-center">{row.meeting}</td>
                      <td className="p-4 text-indigo-700 font-bold">{row.cplCode}</td>
                      <td className="p-4 text-slate-400">{row.cpmkCode}</td>
                      <td className="p-4 text-blue-600">{row.subCode}</td>
                      <td className="p-4">{row.name}</td>
                      <td className="p-4 text-center font-bold">{row.weight}%</td>
                      <td className="p-4 text-center">{row.score}</td>
                      <td className="p-4 text-center bg-blue-50 text-blue-700">{row.weightedScore.toFixed(2)}</td>
                      <td className="p-4 text-center bg-indigo-50 text-indigo-700">{row.achievement.toFixed(1)}%</td>
                      <td className="p-4 text-center">{row.status === 'REMEDIAL' ? <span className="text-rose-600 font-bold">REMEDIAL</span> : 'TUNTAS'}</td>
                    </tr>
                  ))}
                  <tr className="bg-slate-900 text-white font-black">
                    <td colSpan="5" className="p-5 text-right uppercase text-[9px]">Total Bobot Mata Kuliah</td>
                    <td className="p-5 text-center bg-slate-800">{totalWeightValue}%</td>
                    <td colSpan="1" className="p-5 text-right uppercase text-[9px]">Nilai Akhir Mahasiswa</td>
                    <td className="p-5 text-center bg-blue-700 text-xl font-bold" colSpan="3">{finalScoreValue.toFixed(2)}</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        ) : <div className="py-40 text-center text-slate-300 font-black uppercase text-sm italic">Pilih MK & Mahasiswa untuk Preview Portofolio</div>}
      </div>
    </div>
  );
};

const AnalysisManager = ({ courses, gradingConfigs, studentGrades, activeModel, setActiveModel, academicSettings, embedView = false, externalCourseId = '' }) => {
  const [internalCourseId, setInternalCourseId] = useState('');
  const courseId = embedView ? externalCourseId : internalCourseId;
  const course = useMemo(() => courses.find(c => c.id === courseId), [courses, courseId]);
  const currentConfig = useMemo(() => gradingConfigs.find(c => c.courseId === courseId && c.modelType === activeModel), [gradingConfigs, courseId, activeModel]);
  const students = useMemo(() => studentGrades.filter(s => s.courseId === courseId && s.model === activeModel), [studentGrades, courseId, activeModel]);

  const getGradeCategory = (score) => {
    if (score >= 85) return 'A';
    if (score >= 70) return 'B';
    if (score >= 55) return 'C';
    if (score >= 40) return 'D';
    return 'E';
  };

  const getCategoryColor = (grade) => {
    switch(grade) {
      case 'A': return 'text-emerald-600';
      case 'B': return 'text-blue-600';
      case 'C': return 'text-amber-600';
      case 'D': return 'text-orange-600';
      default: return 'text-rose-600';
    }
  };

  const cplStructure = useMemo(() => {
    if (!currentConfig?.components) return {};
    const structure = {};
    currentConfig.components.forEach(comp => {
      if (!structure[comp.cplCode]) { structure[comp.cplCode] = { components: [], totalWeight: 0 }; }
      structure[comp.cplCode].components.push(comp);
      structure[comp.cplCode].totalWeight += Number(comp.weight);
    });
    return structure;
  }, [currentConfig]);

  const activeCPLCodes = useMemo(() => Object.keys(cplStructure).sort(), [cplStructure]);

  const columnAverages = useMemo(() => {
    if (students.length === 0) return {};
    const results = {};
    activeCPLCodes.forEach(cpl => {
      cplStructure[cpl].components.forEach(comp => {
        const sum = students.reduce((acc, s) => acc + (s.scores?.[comp.id] || 0), 0);
        results[`comp_${comp.id}`] = sum / (students.length || 1);
      });
      const cplScores = students.map(s => {
        const comps = cplStructure[cpl].components;
        const compAvg = comps.reduce((acc, curr) => acc + (s.scores?.[curr.id] || 0), 0) / (comps.length || 1);
        if (activeModel === '2') {
           const partScore = s.scores?.['fixed_participation'] || 0;
           return (compAvg * 0.85) + (partScore * 0.15);
        }
        return compAvg;
      });
      results[`avg_${cpl}`] = cplScores.reduce((acc, val) => acc + val, 0) / (students.length || 1);
      if (activeModel === '2') {
        const partSum = students.reduce((acc, s) => acc + (s.scores?.['fixed_participation'] || 0), 0);
        results[`part_${cpl}`] = partSum / (students.length || 1);
      }
    });
    return results;
  }, [students, activeCPLCodes, cplStructure, activeModel]);

  return (
    <div className="space-y-8 animate-in fade-in duration-500">
      {!embedView && (
        <div className="bg-slate-900 p-8 rounded-[40px] text-white no-print flex flex-col md:flex-row gap-6 items-end justify-between shadow-xl">
           <div className="flex-1 w-full">
              <label className="text-[10px] font-black uppercase text-slate-500 mb-2 block tracking-widest">Analisis Mata Kuliah</label>
              <select value={internalCourseId} onChange={e => setInternalCourseId(e.target.value)} className="w-full p-4 bg-slate-800 border border-slate-700 rounded-2xl font-black text-lg outline-none focus:border-blue-500">
                <option value="">-- Pilih Mata Kuliah --</option>
                {courses.map(c => <option key={c.id} value={c.id}>{c.code} - {c.name}</option>)}
              </select>
           </div>
           <div className="flex bg-slate-800 p-1 rounded-2xl">
              <button onClick={() => setActiveModel('1')} className={`px-8 py-3 rounded-xl text-xs font-black transition-all ${activeModel === '1' ? 'bg-blue-600 text-white shadow-lg' : 'text-slate-500'}`}>MODEL 1</button>
              <button onClick={() => setActiveModel('2')} className={`px-8 py-3 rounded-xl text-xs font-black transition-all ${activeModel === '2' ? 'bg-blue-600 text-white shadow-lg' : 'text-slate-500'}`}>MODEL 2</button>
           </div>
        </div>
      )}
      <div id="report-content" className="space-y-6">
        {courseId && currentConfig ? (
          <div className="space-y-6">
            {!embedView && (
              <div className="bg-white p-10 rounded-[40px] border shadow-sm text-center space-y-2 relative overflow-hidden">
                 <div className="absolute top-0 left-0 p-8 opacity-5"><BarChart3 size={100}/></div>
                 <h1 className="text-xl font-black uppercase text-slate-900">DAFTAR ANALISIS</h1>
                 <h2 className="text-lg font-bold uppercase text-slate-700">PENCAPAIAN CPL PADA MATA KULIAH</h2>
                 <h3 className="text-2xl font-black text-blue-600 uppercase underline underline-offset-8 decoration-blue-100">{course?.name}</h3>
                 <div className="pt-4"><span className="px-6 py-2 bg-slate-900 text-white rounded-full font-black text-xs uppercase tracking-widest">TAHUN AKADEMIK {academicSettings.year} SEMESTER {academicSettings.semester}</span></div>
            </div>
            )}
            <div className="bg-white rounded-[40px] border border-slate-100 overflow-hidden shadow-md overflow-x-auto">
               <table className="w-full text-left text-[10px] border-collapse" border="1" cellPadding="5">
                  <thead>
                     <tr className="bg-slate-900 text-white">
                        <th rowSpan="3" className="p-4 border-r border-slate-800 text-center w-12">No</th>
                        <th rowSpan="3" className="p-4 border-r border-slate-800 min-w-[180px]">Nama Mahasiswa</th>
                        <th rowSpan="3" className="p-4 border-r border-slate-800 text-center w-28">NIM</th>
                        {activeCPLCodes.map(cpl => {
                           const model2ExtraCol = activeModel === '2' ? 1 : 0;
                           return (<th key={cpl} colSpan={cplStructure[cpl].components.length + 2 + model2ExtraCol} className="p-3 border-r border-slate-800 text-center bg-blue-800 font-black uppercase tracking-widest border-b border-blue-700">{cpl}</th>);
                        })}
                     </tr>
                     <tr className="bg-slate-800 text-white">
                        {activeCPLCodes.map(cpl => (
                           <React.Fragment key={`sub_${cpl}`}>
                              {cplStructure[cpl].components.map(comp => (<th key={comp.id} className="p-2 border-r border-slate-700 text-center font-bold text-[9px] min-w-[70px]">{comp.subCode}</th>))}
                              {activeModel === '2' && (<th className="p-2 border-r border-slate-700 text-center bg-emerald-800 font-bold text-[9px] min-w-[70px]">Partisipasi</th>)}
                              <th className="p-2 border-r border-slate-700 text-center bg-indigo-900 font-black min-w-[80px]">Skor Capaian</th>
                              <th className="p-2 border-r border-slate-700 text-center bg-indigo-950 font-black min-w-[60px]">Kategori</th>
                           </React.Fragment>
                        ))}
                     </tr>
                     <tr className="bg-slate-100 text-slate-500 font-black">
                        {activeCPLCodes.map(cpl => (
                           <React.Fragment key={`weight_${cpl}`}>
                              {cplStructure[cpl].components.map(comp => (<th key={`w_${comp.id}`} className="p-1.5 border-r border-slate-200 text-center text-[8px]">({comp.weight}%)</th>))}
                              {activeModel === '2' && (<th className="p-1.5 border-r border-slate-200 text-center text-[8px]">(15%)</th>)}
                              <th className="p-1.5 border-r border-slate-200 text-center">-</th>
                              <th className="p-1.5 border-r border-slate-200 text-center">-</th>
                           </React.Fragment>
                        ))}
                     </tr>
                  </thead>
                  <tbody className="divide-y divide-slate-100 font-bold text-slate-700">
                     {students.map((s, idx) => (
                        <tr key={s.id} className="hover:bg-slate-50 transition-colors">
                           <td className="p-3 text-center border-r border-slate-50 bg-slate-50/50 font-medium">{idx + 1}</td>
                           <td className="p-3 border-r border-slate-50 uppercase text-[11px] truncate font-bold">{s.name}</td>
                           <td className="p-3 border-r border-slate-50 text-center text-slate-400 font-medium">{s.nim}</td>
                           {activeCPLCodes.map(cpl => {
                              const comps = cplStructure[cpl].components;
                              const compAvg = comps.reduce((acc, curr) => acc + (s.scores?.[curr.id] || 0), 0) / (comps.length || 1);
                              let finalValueResult = compAvg;
                              if (activeModel === '2') { const partScore = s.scores?.['fixed_participation'] || 0; finalValueResult = (compAvg * 0.85) + (partScore * 0.15); }
                              const grade = getGradeCategory(finalValueResult);
                              return (
                                 <React.Fragment key={`val_${s.id}_${cpl}`}>
                                    {comps.map(comp => (<td key={`sc_${s.id}_${comp.id}`} className="p-3 border-r border-slate-50 text-center">{s.scores?.[comp.id] || 0}</td>))}
                                    {activeModel === '2' && (<td className="p-3 border-r border-slate-50 text-center bg-emerald-50/30 text-emerald-700 font-bold">{s.scores?.['fixed_participation'] || 0}</td>)}
                                    <td className="p-3 border-r border-slate-50 text-center bg-indigo-50/30 text-indigo-700 font-black text-xs">{finalValueResult.toFixed(1)}</td>
                                    <td className={`p-3 border-r border-slate-50 text-center font-black ${getCategoryColor(grade)}`}>{grade}</td>
                                 </React.Fragment>
                              );
                           })}
                        </tr>
                     ))}
                     <tr className="bg-slate-900 text-white font-black text-[11px]">
                        <td colSpan="3" className="p-4 text-right uppercase tracking-[0.1em]">Rerata Mahasiswa</td>
                        {activeCPLCodes.map(cpl => (
                           <React.Fragment key={`avg_footer_${cpl}`}>
                              {cplStructure[cpl].components.map(comp => (<td key={`fcomp_${comp.id}`} className="p-4 text-center border-r border-slate-800 text-blue-300">{columnAverages[`comp_${comp.id}`]?.toFixed(1) || '0.0'}</td>))}
                              {activeModel === '2' && (<td className="p-4 text-center border-r border-slate-800 text-emerald-300 bg-emerald-950/20">{columnAverages[`part_${cpl}`]?.toFixed(1) || '0.0'}</td>)}
                              <td className="p-4 text-center border-r border-slate-800 bg-indigo-800 text-white text-lg font-bold">{columnAverages[`avg_${cpl}`]?.toFixed(1) || '0.0'}</td>
                              <td className="p-4 text-center border-r border-slate-800 bg-indigo-900 text-white font-bold">{getGradeCategory(columnAverages[`avg_${cpl}`] || 0)}</td>
                           </React.Fragment>
                        ))}
                     </tr>
                  </tbody>
               </table>
            </div>
          </div>
        ) : (
          <div className="flex flex-col items-center justify-center py-40 bg-white border-2 border-dashed rounded-[48px] text-slate-300">
             <Award size={64} className="mb-4 opacity-10"/>
             <p className="font-black uppercase tracking-[0.3em] text-sm text-center">Pilih Mata Kuliah untuk Melihat Analisis Capaian</p>
          </div>
        )}
      </div>
    </div>
  );
};

const DownloadManager = ({ courses, gradingConfigs, studentGrades, activeModel, setActiveModel, academicSettings, institutionData, profileData }) => {
  const [selectedCourseId, setSelectedCourseId] = useState('');
  const [selectedStudentId, setSelectedStudentId] = useState('');
  const [printType, setPrintType] = useState('portfolio');
  const studentsInCourse = studentGrades.filter(s => s.courseId === selectedCourseId && s.model === activeModel);
  const course = courses.find(c => c.id === selectedCourseId);

  const exportToWord = () => {
    const reportElement = document.getElementById('report-to-download');
    if (!reportElement) return;

    // METADATA WORD LANDSCAPE & HIGH FIDELITY CSS
    const header = `
      <html xmlns:o='urn:schemas-microsoft-com:office:office' 
            xmlns:w='urn:schemas-microsoft-com:office:word' 
            xmlns='http://www.w3.org/TR/REC-html40'>
      <head>
        <meta charset='utf-8'>
        <title>SIMOBE Export</title>
        <!--[if gte mso 9]>
        <xml>
          <w:WordDocument>
            <w:View>Print</w:View>
            <w:Zoom>100</w:Zoom>
          </w:WordDocument>
        </xml>
        <![endif]-->
        <style>
          @page {
            size: A4 landscape;
            margin: 1.5cm 1.5cm 1.5cm 1.5cm;
            mso-header-margin: 35.4pt;
            mso-footer-margin: 35.4pt;
            mso-page-orientation: landscape;
          }
          body { font-family: 'Segoe UI', Arial, sans-serif; font-size: 10pt; color: #334155; }
          .header-box { text-align: center; border-bottom: 3px solid #0f172a; padding-bottom: 10px; margin-bottom: 20px; }
          .title-lg { font-size: 18pt; font-weight: bold; text-transform: uppercase; color: #0f172a; margin: 0; }
          .title-md { font-size: 14pt; font-weight: bold; text-transform: uppercase; color: #475569; margin: 0; }
          .title-sm { font-size: 11pt; font-weight: bold; text-transform: uppercase; color: #2563eb; margin-top: 5px; }
          .subtitle { font-size: 9pt; font-weight: bold; color: #64748b; text-transform: uppercase; }
          
          table { width: 100%; border-collapse: collapse; margin-bottom: 20px; }
          th, td { border: 1px solid #cbd5e1; padding: 10px; text-align: left; }
          
          th { background-color: #0f172a !important; color: white !important; font-weight: bold; text-transform: uppercase; font-size: 8pt; }
          .bg-blue-600 { background-color: #2563eb !important; color: white !important; }
          .bg-indigo-800 { background-color: #3730a3 !important; color: white !important; }
          .bg-indigo-900 { background-color: #312e81 !important; color: white !important; }
          .bg-indigo-950 { background-color: #1e1b4b !important; color: white !important; }
          .bg-emerald-800 { background-color: #065f46 !important; color: white !important; }
          .bg-slate-900 { background-color: #0f172a !important; color: white !important; }
          
          .text-center { text-align: center; }
          .font-black { font-weight: bold; }
          .uppercase { text-transform: uppercase; }
          .text-blue-600 { color: #2563eb; }
          .text-indigo-700 { color: #4338ca; }
          .text-emerald-700 { color: #047857; }
          .text-rose-600 { color: #e11d48; }
          
          .badge { padding: 4px 10px; border-radius: 99px; font-size: 8pt; font-weight: bold; }
          .badge-blue { background-color: #eff6ff; color: #2563eb; border: 1px solid #dbeafe; }
          .badge-slate { background-color: #f1f5f9; color: #64748b; border: 1px solid #e2e8f0; }
        </style>
      </head>
      <body>
    `;
    const footer = `</body></html>`;
    const content = reportElement.innerHTML;
    const blob = new Blob(['\ufeff', header + content + footer], { type: 'application/msword' });
    const url = URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = url;
    link.download = `SIMOBE_${printType.toUpperCase()}_${course?.code || 'EXPORT'}.doc`;
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  };

  return (
    <div className="space-y-8 animate-in fade-in duration-500">
      <div className="bg-slate-900 p-8 rounded-[40px] text-white no-print shadow-xl">
        <div className="flex flex-col md:flex-row gap-6 items-end justify-between">
          <div className="flex-1 w-full grid grid-cols-1 md:grid-cols-4 gap-4">
            <div>
              <label className="text-[10px] font-black uppercase text-slate-500 mb-2 block tracking-widest">Tipe Dokumen</label>
              <select value={printType} onChange={e => setPrintType(e.target.value)} className="w-full p-4 bg-slate-800 border-slate-700 border rounded-2xl font-bold outline-none">
                <option value="portfolio">Portofolio Mahasiswa</option>
                <option value="analysis">Analisis Capaian CPL</option>
              </select>
            </div>
            <div>
              <label className="text-[10px] font-black uppercase text-slate-500 mb-2 block tracking-widest">Mata Kuliah</label>
              <select value={selectedCourseId} onChange={e => { setSelectedCourseId(e.target.value); setSelectedStudentId(''); }} className="w-full p-4 bg-slate-800 border-slate-700 border rounded-2xl font-bold outline-none">
                <option value="">-- Pilih MK --</option>
                {courses.map(c => <option key={c.id} value={c.id}>{c.code} - {c.name}</option>)}
              </select>
            </div>
            {printType === 'portfolio' && (
              <div>
                <label className="text-[10px] font-black uppercase text-slate-500 mb-2 block tracking-widest">Mahasiswa</label>
                <select value={selectedStudentId} onChange={e => setSelectedStudentId(e.target.value)} className="w-full p-4 bg-slate-800 border-slate-700 border rounded-2xl font-bold outline-none">
                  <option value="">-- Pilih Mahasiswa --</option>
                  {studentsInCourse.map(s => <option key={s.id} value={s.id}>{s.name} ({s.nim})</option>)}
                </select>
              </div>
            )}
            <div className="flex items-end gap-2">
              <button 
                onClick={exportToWord} 
                disabled={!selectedCourseId || (printType === 'portfolio' && !selectedStudentId)} 
                className="w-full bg-blue-600 text-white py-4 rounded-2xl font-black uppercase text-xs tracking-widest shadow-xl flex items-center justify-center gap-2 hover:bg-blue-700 transition-all disabled:opacity-40"
              >
                <FileOutput size={18}/> Download Dokumen Word
              </button>
            </div>
          </div>
          <div className="flex bg-slate-800 p-1 rounded-2xl">
            <button onClick={() => setActiveModel('1')} className={`px-6 py-2 rounded-xl text-[10px] font-black transition-all ${activeModel === '1' ? 'bg-blue-600 text-white' : 'text-slate-400'}`}>MODEL 1</button>
            <button onClick={() => setActiveModel('2')} className={`px-6 py-2 rounded-xl text-[10px] font-black transition-all ${activeModel === '2' ? 'bg-blue-600 text-white' : 'text-slate-400'}`}>MODEL 2</button>
          </div>
        </div>
      </div>

      <div id="report-to-download" className="bg-white p-12 rounded-[40px] border shadow-sm print:shadow-none print:border-none print:p-0 min-h-[600px] transition-all">
        {selectedCourseId ? (
          <div className="animate-in fade-in zoom-in-95 duration-500">
            {/* Header Laporan */}
            <div className="header-box">
               <h1 className="title-lg">{institutionData.university || 'UNIVERSITAS'}</h1>
               <h2 className="title-md">{institutionData.faculty || 'FAKULTAS'}</h2>
               <h3 className="title-md">{institutionData.department || 'JURUSAN'}</h3>
               <p className="title-sm">PROGRAM STUDI {institutionData.program || 'PRODI'}</p>
               <br/>
               <p style={{textDecoration: 'underline', fontWeight: 'bold', fontSize: '11pt', textTransform: 'uppercase'}}>
                 LAPORAN {printType === 'portfolio' ? 'PORTOFOLIO CAPAIAN MAHASISWA' : 'ANALISIS CAPAIAN CPL MATA KULIAH'}
               </p>
               <p className="subtitle">Tahun Akademik {academicSettings.year} - {academicSettings.semester}</p>
            </div>

            {/* Konten Laporan */}
            <div id="actual-report-content">
              {printType === 'portfolio' ? (
                <PortfolioManager courses={courses} gradingConfigs={gradingConfigs} studentGrades={studentGrades} activeModel={activeModel} setActiveModel={setActiveModel} embedView={true} externalCourseId={selectedCourseId} externalStudentId={selectedStudentId} />
              ) : (
                <AnalysisManager courses={courses} gradingConfigs={gradingConfigs} studentGrades={studentGrades} activeModel={activeModel} setActiveModel={setActiveModel} academicSettings={academicSettings} embedView={true} externalCourseId={selectedCourseId} />
              )}
            </div>
          </div>
        ) : (
          <div className="flex flex-col items-center justify-center py-40 text-slate-300">
            <FileOutput size={64} className="mb-4 opacity-10"/>
            <p className="font-black uppercase tracking-[0.3em] text-sm text-center">Pilih MK untuk Preview Dokumen</p>
          </div>
        )}
      </div>
    </div>
  );
};

const BackupManager = ({ user, cpls, courses, gradingConfigs, studentGrades, academicSettings, institutionData, profileData }) => {
  const [status, setStatus] = useState({ message: '', type: '' });
  const fileInputRef = useRef(null);
  
  const exportData = async () => {
    try {
      const backupObj = { 
        version: "1.24", 
        exportDate: new Date().toISOString(), 
        collections: { cpls, courses, grading_configs: gradingConfigs, student_grades: studentGrades }, 
        configs: { academic: academicSettings, institution: institutionData, profile: profileData } 
      };
      const dataStr = JSON.stringify(backupObj, null, 2);
      const link = document.createElement('a'); 
      link.setAttribute('href', 'data:application/json;charset=utf-8,'+ encodeURIComponent(dataStr));
      link.setAttribute('download', `SIMOBE_BACKUP_${new Date().toISOString().split('T')[0]}.json`); 
      link.click();
      setStatus({ message: 'Ekspor Berhasil!', type: 'success' });
      setTimeout(() => setStatus({message: '', type: ''}), 3000);
    } catch (e) { setStatus({ message: 'Ekspor Gagal!', type: 'error' }); }
  };

  const importData = async (e) => {
    const file = e.target.files[0]; if (!file) return;
    const reader = new FileReader();
    reader.onload = async (evt) => {
      try {
        const imported = JSON.parse(evt.target.result);
        const userId = user.uid;
        const appId = typeof __app_id !== 'undefined' ? __app_id : APP_ID;
        const getRef = (coll, id) => doc(db, 'artifacts', appId, 'users', userId, coll, id);
        
        // Helper function to commit batches in chunks of 400
        const commitInChunks = async (items, collectionName) => {
          if (!items || items.length === 0) return;
          const chunkSize = 400;
          for (let i = 0; i < items.length; i += chunkSize) {
            const batch = writeBatch(db);
            const chunk = items.slice(i, i + chunkSize);
            chunk.forEach(item => {
              if (item.id) batch.set(getRef(collectionName, item.id), item);
            });
            await batch.commit();
          }
        };

        // Restore Configs (Single Batch)
        const configBatch = writeBatch(db);
        if (imported.configs?.academic) configBatch.set(getRef('config', 'academic'), imported.configs.academic);
        if (imported.configs?.institution) configBatch.set(getRef('config', 'institution'), imported.configs.institution);
        if (imported.configs?.profile) configBatch.set(getRef('config', 'profile'), imported.configs.profile);
        await configBatch.commit();
        
        // Restore Collections with Chunking
        await commitInChunks(imported.collections?.cpls, 'cpls');
        await commitInChunks(imported.collections?.courses, 'courses');
        await commitInChunks(imported.collections?.grading_configs, 'grading_configs');
        await commitInChunks(imported.collections?.student_grades, 'student_grades');
        
        setStatus({ message: 'Data Berhasil Dipulihkan!', type: 'success' });
        setTimeout(() => setStatus({message: '', type: ''}), 3000);
      } catch (err) { 
        console.error(err);
        setStatus({ message: 'Impor Gagal! Cek format file.', type: 'error' }); 
      }
    };
    reader.readAsText(file);
  };

  return (
    <div className="space-y-8 animate-in fade-in duration-500 max-w-6xl mx-auto">
      <div className="bg-slate-900 p-10 rounded-[40px] text-white shadow-2xl flex items-center justify-between relative overflow-hidden">
        <div className="relative z-10">
          <h3 className="text-3xl font-black mb-2 uppercase tracking-tight">Backup dan Restore</h3>
          <p className="text-slate-400 font-bold text-sm max-w-md">Amankan data kurikulum dan penilaian Anda atau pulihkan dari file cadangan sebelumnya.</p>
        </div>
        <Database size={100} className="opacity-10 absolute right-10 top-1/2 -translate-y-1/2" />
      </div>

      <div className="grid grid-cols-1 md:grid-cols-3 gap-8">
        <div className="md:col-span-2 grid grid-cols-1 sm:grid-cols-2 gap-6">
          <button onClick={exportData} className="bg-white p-10 rounded-[40px] border shadow-sm flex flex-col items-center text-center group hover:border-blue-200 transition-all hover:shadow-lg">
            <div className="w-20 h-20 bg-blue-50 text-blue-600 rounded-3xl flex items-center justify-center mb-6 group-hover:scale-110 transition-transform">
              <HardDriveDownload size={32} />
            </div>
            <span className="font-black uppercase tracking-widest text-slate-800">Ekspor Data</span>
            <p className="text-[10px] text-slate-400 mt-2 font-bold uppercase">Unduh semua data ke dalam satu file .json</p>
          </button>
          
          <button onClick={() => fileInputRef.current?.click()} className="bg-white p-10 rounded-[40px] border shadow-sm flex flex-col items-center text-center group hover:border-emerald-200 transition-all hover:shadow-lg">
            <div className="w-20 h-20 bg-emerald-50 text-emerald-600 rounded-3xl flex items-center justify-center mb-6 group-hover:scale-110 transition-transform">
              <HardDriveUpload size={32} />
            </div>
            <span className="font-black uppercase tracking-widest text-slate-800">Pulihkan Data</span>
            <p className="text-[10px] text-slate-400 mt-2 font-bold uppercase">Unggah file cadangan untuk mengembalikan data</p>
            <input type="file" ref={fileInputRef} onChange={importData} className="hidden" accept=".json" />
          </button>
        </div>

        <div className="bg-blue-600/5 border border-blue-100 p-8 rounded-[40px] flex flex-col gap-6">
           <div className="flex items-center gap-3 text-blue-700">
              <HelpCircle size={24} />
              <h4 className="font-black uppercase text-sm tracking-widest">Informasi Penting</h4>
           </div>
           <div className="space-y-4">
              <div className="flex gap-4">
                 <div className="w-6 h-6 rounded-full bg-blue-600 text-white flex-shrink-0 flex items-center justify-center text-[10px] font-black">1</div>
                 <p className="text-xs text-slate-600 font-bold leading-relaxed">Pencadangan data (Ekspor) sangat disarankan dilakukan secara berkala.</p>
              </div>
              <div className="flex gap-4">
                 <div className="w-6 h-6 rounded-full bg-blue-600 text-white flex-shrink-0 flex items-center justify-center text-[10px] font-black">2</div>
                 <p className="text-xs text-slate-600 font-bold leading-relaxed">Proses Restore akan menimpa data yang ada saat ini secara permanen.</p>
              </div>
              <div className="flex gap-4">
                 <div className="w-6 h-6 rounded-full bg-blue-600 text-white flex-shrink-0 flex items-center justify-center text-[10px] font-black">3</div>
                 <p className="text-xs text-slate-600 font-bold leading-relaxed">Data yang dicadangkan meliputi: Profil, Institusi, Kurikulum, dan Nilai.</p>
              </div>
           </div>
           {status.message && (
             <div className={`mt-auto p-4 rounded-2xl flex items-center gap-3 animate-bounce ${status.type === 'success' ? 'bg-emerald-100 text-emerald-700' : 'bg-rose-100 text-rose-700'}`}>
                {status.type === 'success' ? <CheckCircle2 size={18}/> : <AlertCircle size={18}/>}
                <span className="text-xs font-black uppercase tracking-widest">{status.message}</span>
             </div>
           )}
        </div>
      </div>
    </div>
  );
};

const ProfileModal = ({ profileData, institutionData, onClose, onSave }) => {
  const [activeTab, setActiveTab] = useState('dosen'); 
  const [localProfile, setLocalProfile] = useState(profileData || {});
  const [localInst, setLocalInst] = useState(institutionData || {});

  useEffect(() => { 
    if (profileData) setLocalProfile(profileData); 
    if (institutionData) setLocalInst(institutionData); 
  }, [profileData, institutionData]);

  const handlePhotoChange = (e) => { 
    const file = e.target.files[0]; 
    if (!file) return; 
    const reader = new FileReader(); 
    reader.onloadend = () => { setLocalProfile({ ...localProfile, photo: reader.result }); }; 
    reader.readAsDataURL(file); 
  };

  const handleLogoChange = (e) => { 
    const file = e.target.files[0]; 
    if (!file) return; 
    const reader = new FileReader(); 
    reader.onloadend = () => { setLocalProfile({ ...localProfile, appLogo: reader.result }); }; 
    reader.readAsDataURL(file); 
  };

  return (
    <div className="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4">
      <div className="bg-white rounded-[40px] shadow-2xl w-full max-w-lg overflow-hidden animate-in zoom-in duration-300 flex flex-col max-h-[95vh]">
        <div className="bg-slate-900 p-8 text-white relative flex-shrink-0">
          <button onClick={onClose} className="absolute top-6 right-6 text-slate-400 hover:text-white"><X size={24} /></button>
          <h3 className="text-xl font-black uppercase tracking-tight">Pengaturan Identitas</h3>
          <div className="flex gap-2 mt-6 p-1 bg-slate-800 rounded-xl">
            <button onClick={() => setActiveTab('dosen')} className={`flex-1 py-2 rounded-lg text-[10px] font-black uppercase transition-all ${activeTab === 'dosen' ? 'bg-blue-600 text-white shadow-md' : 'text-slate-400'}`}>Dosen</button>
            <button onClick={() => setActiveTab('institusi')} className={`flex-1 py-2 rounded-lg text-[10px] font-black uppercase transition-all ${activeTab === 'institusi' ? 'bg-blue-600 text-white shadow-md' : 'text-slate-400'}`}>Institusi</button>
          </div>
        </div>
        
        <div className="p-8 space-y-6 overflow-y-auto flex-1 scrollbar-hide">
           {activeTab === 'dosen' ? (
             <div className="space-y-6 animate-in slide-in-from-left-4 duration-300">
                <div className="flex gap-6 justify-center">
                    <div className="flex flex-col items-center gap-2">
                      <div className="w-24 h-24 rounded-full border-2 border-slate-100 overflow-hidden bg-slate-50 flex items-center justify-center cursor-pointer shadow-inner" onClick={() => document.getElementById('pf-input').click()}>
                        {localProfile.photo ? <img src={localProfile.photo} className="w-full h-full object-cover" alt="Profile"/> : <UserCircle size={48} className="text-slate-300"/>}
                      </div>
                      <span className="text-[9px] font-black uppercase text-slate-400 tracking-widest">Pas Foto</span>
                    </div>
                    <div className="flex flex-col items-center gap-2">
                      <div className="w-24 h-24 rounded-[24px] border-2 border-slate-100 overflow-hidden bg-slate-50 flex items-center justify-center cursor-pointer shadow-inner" onClick={() => document.getElementById('lg-input').click()}>
                        {localProfile.appLogo ? <img src={localProfile.appLogo} className="w-full h-full object-contain p-2" alt="Logo"/> : <Image size={32} className="text-slate-300"/>}
                      </div>
                      <span className="text-[9px] font-black uppercase text-slate-400 tracking-widest">Logo Aplikasi</span>
                    </div>
                    <input type="file" id="pf-input" onChange={handlePhotoChange} className="hidden" accept="image/*" />
                    <input type="file" id="lg-input" onChange={handleLogoChange} className="hidden" accept="image/*" />
                </div>
                <div className="space-y-4">
                    <div>
                      <label className="text-[10px] font-black text-slate-400 uppercase mb-1 block tracking-widest">Nama Lengkap & Gelar</label>
                      <input value={localProfile.name || ''} onChange={e => setLocalProfile({...localProfile, name: e.target.value})} className="w-full p-4 bg-slate-50 border rounded-2xl font-bold outline-none focus:border-blue-500" placeholder="Contoh: Dr. Nama, M.Si" />
                    </div>
                    <div>
                      <label className="text-[10px] font-black text-slate-400 uppercase mb-1 block tracking-widest">NIDN / NIP / NUPTK</label>
                      <input value={localProfile.nidn || ''} onChange={e => setLocalProfile({...localProfile, nidn: e.target.value})} className="w-full p-4 bg-slate-50 border rounded-2xl font-bold outline-none focus:border-blue-500" placeholder="0011223344" />
                    </div>
                </div>
             </div>
           ) : (
             <div className="space-y-4 animate-in slide-in-from-right-4 duration-300">
                <div>
                  <label className="text-[10px] font-black text-slate-400 uppercase mb-1 block tracking-widest">Perguruan Tinggi</label>
                  <input value={localInst.university || ''} onChange={e => setLocalInst({...localInst, university: e.target.value})} placeholder="Nama Universitas..." className="w-full p-4 bg-slate-50 border rounded-2xl font-bold outline-none focus:border-blue-500" />
                </div>
                <div>
                  <label className="text-[10px] font-black text-slate-400 uppercase mb-1 block tracking-widest">Fakultas / Sekolah</label>
                  <input value={localInst.faculty || ''} onChange={e => setLocalInst({...localInst, faculty: e.target.value})} placeholder="Nama Fakultas..." className="w-full p-4 bg-slate-50 border rounded-2xl font-bold outline-none focus:border-blue-500" />
                </div>
                <div>
                  <label className="text-[10px] font-black text-slate-400 uppercase mb-1 block tracking-widest">Jurusan / Departemen</label>
                  <input value={localInst.department || ''} onChange={e => setLocalInst({...localInst, department: e.target.value})} placeholder="Nama Jurusan..." className="w-full p-4 bg-slate-50 border rounded-2xl font-bold outline-none focus:border-blue-500" />
                </div>
                <div>
                  <label className="text-[10px] font-black text-slate-400 uppercase mb-1 block tracking-widest">Program Studi</label>
                  <input value={localInst.program || ''} onChange={e => setLocalInst({...localInst, program: e.target.value})} placeholder="Nama Prodi..." className="w-full p-4 bg-slate-50 border rounded-2xl font-bold outline-none focus:border-blue-500" />
                </div>
             </div>
           )}
        </div>
        
        <div className="p-8 bg-slate-50 border-t flex gap-3 flex-shrink-0">
          <button onClick={onClose} className="flex-1 py-4 font-black uppercase text-xs text-slate-500 hover:text-slate-800 transition-colors">Batal</button>
          <button onClick={() => onSave(localProfile, localInst)} className="flex-[2] bg-blue-600 text-white py-4 rounded-2xl font-black uppercase text-xs shadow-xl transition-all hover:bg-blue-700 hover:scale-[1.02] active:scale-95">Simpan Perubahan</button>
        </div>
      </div>
    </div>
  );
};

// ==========================================
// MAIN APP COMPONENT
// ==========================================

const App = () => {
  const [user, setUser] = useState(null);
  const [activeTab, setActiveTab] = useState('dashboard');
  const [activeSettingsSubTab, setActiveSettingsSubTab] = useState('cpl-prodi');
  const [activeAssessmentSubTab, setActiveAssessmentSubTab] = useState('1'); 
  const [activeInputModelSubTab, setActiveInputModelSubTab] = useState('1');
  const [activePortfolioModelSubTab, setActivePortfolioModelSubTab] = useState('1');
  const [activeAnalysisSubTab, setActiveAnalysisSubTab] = useState('1');
  const [activePrintModelSubTab, setActivePrintModelSubTab] = useState('1');
  const [loading, setLoading] = useState(true);
  const [showProfileModal, setShowProfileModal] = useState(false);
  
  const [cpls, setCpls] = useState([]);
  const [courses, setCourses] = useState([]);
  const [gradingConfigs, setGradingConfigs] = useState([]);
  const [studentGrades, setStudentGrades] = useState([]);
  const [academicSettings, setAcademicSettings] = useState({ year: '2024/2025', semester: 'Ganjil' });
  const [institutionData, setInstitutionData] = useState({ university: '', faculty: '', department: '', program: '' });
  const [profileData, setProfileData] = useState({ name: '', nidn: '', photo: null, appLogo: null });

  const isPrintEnabled = useMemo(() => courses.length > 0, [courses]);

  // Auth & Init
  useEffect(() => {
    const initAuth = async () => {
      try {
        if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
          await signInWithCustomToken(auth, __initial_auth_token);
        } else {
          await signInAnonymously(auth);
        }
      } catch (e) {
        console.error("Auth init failed", e);
      }
    };
    initAuth();
    const unsub = onAuthStateChanged(auth, (u) => { 
      if (u) { 
        setUser(u); 
        setLoading(false); 
      } 
    });
    return () => unsub();
  }, []);

  // Data Listeners
  useEffect(() => {
    if (!user) return;
    const appId = typeof __app_id !== 'undefined' ? __app_id : APP_ID;
    const p = (coll) => collection(db, 'artifacts', appId, 'users', user.uid, coll);
    const d = (coll, id) => doc(db, 'artifacts', appId, 'users', user.uid, coll, id);
    
    // Using onError callback in onSnapshot to handle potential permission/connection errors
    const onError = (err) => console.error("Snapshot error:", err);

    const unsubCpls = onSnapshot(p('cpls'), (snap) => setCpls(snap.docs.map(doc => ({ id: doc.id, ...doc.data() }))), onError);
    const unsubCourses = onSnapshot(p('courses'), (snap) => setCourses(snap.docs.map(doc => ({ id: doc.id, ...doc.data() }))), onError);
    const unsubGrading = onSnapshot(p('grading_configs'), (snap) => setGradingConfigs(snap.docs.map(doc => ({ id: doc.id, ...doc.data() }))), onError);
    const unsubStudents = onSnapshot(p('student_grades'), (snap) => setStudentGrades(snap.docs.map(doc => ({ id: doc.id, ...doc.data() }))), onError);
    const unsubAcad = onSnapshot(d('config', 'academic'), (doc) => doc.exists() && setAcademicSettings(doc.data()), onError);
    const unsubInst = onSnapshot(d('config', 'institution'), (doc) => doc.exists() && setInstitutionData(doc.data()), onError);
    const unsubProf = onSnapshot(d('config', 'profile'), (doc) => doc.exists() && setProfileData(doc.data()), onError);
    
    return () => { unsubCpls(); unsubCourses(); unsubGrading(); unsubStudents(); unsubAcad(); unsubInst(); unsubProf(); };
  }, [user]);

  const saveData = async (coll, docId, data) => { 
    if (!user) return; 
    const appId = typeof __app_id !== 'undefined' ? __app_id : APP_ID; 
    await setDoc(doc(db, 'artifacts', appId, 'users', user.uid, coll, docId), data); 
  };
  
  const deleteData = async (coll, docId) => { 
    if (!user) return; 
    const appId = typeof __app_id !== 'undefined' ? __app_id : APP_ID; 
    await deleteDoc(doc(db, 'artifacts', appId, 'users', user.uid, coll, docId)); 
  };

  if (loading) return <div className="h-screen flex items-center justify-center font-black bg-slate-50 text-slate-300">SIMOBE LOADING...</div>;

  const renderContent = () => {
    switch (activeTab) {
      case 'dashboard': return <DashboardPlaceholder courses={courses} cpls={cpls} institutionData={institutionData} academicSettings={academicSettings} profileData={profileData} />;
      case 'pengaturan': return (
        <div className="flex flex-col gap-6">
          <div className="flex flex-wrap gap-2">
            <SubNavItem id="cpl-prodi" icon={<Trophy size={16}/>} label="1. CPL Prodi" active={activeSettingsSubTab === 'cpl-prodi'} onClick={setActiveSettingsSubTab} colorTheme="indigo" />
            <SubNavItem id="mata-kuliah" icon={<BookOpen size={16}/>} label="2. Mata Kuliah" active={activeSettingsSubTab === 'mata-kuliah'} onClick={setActiveSettingsSubTab} colorTheme="emerald" />
            <SubNavItem id="pemetaan-cpl" icon={<Link2 size={16}/>} label="3. Mapping CPL" active={activeSettingsSubTab === 'pemetaan-cpl'} onClick={setActiveSettingsSubTab} colorTheme="violet" />
            <SubNavItem id="cpmk" icon={<ListTodo size={16}/>} label="4. CPMK & Sub" active={activeSettingsSubTab === 'cpmk'} onClick={setActiveSettingsSubTab} colorTheme="rose" />
          </div>
          <div className="bg-white rounded-[40px] border p-8 shadow-sm">
            {activeSettingsSubTab === 'cpl-prodi' && <CplManager cpls={cpls} onSave={(id, data) => saveData('cpls', id, data)} onDelete={(id) => deleteData('cpls', id)} />}
            {activeSettingsSubTab === 'mata-kuliah' && <CourseManager courses={courses} academicSettings={academicSettings} onSaveCourse={(id, data) => saveData('courses', id, data)} onDeleteCourse={(id) => deleteData('courses', id)} onSaveAcademic={(data) => saveData('config', 'academic', data)} />}
            {activeSettingsSubTab === 'pemetaan-cpl' && <MappingManager courses={courses} cpls={cpls} onSave={(id, data) => saveData('courses', id, data)} />}
            {activeSettingsSubTab === 'cpmk' && <CpmkManager courses={courses} onSave={(id, data) => saveData('courses', id, data)} />}
          </div>
        </div>
      );
      case 'assessments': return <GradingSystem courses={courses} onSaveConfig={(id, data) => saveData('grading_configs', id, data)} gradingConfigs={gradingConfigs} activeModel={activeAssessmentSubTab} setActiveModel={setActiveAssessmentSubTab} />;
      case 'input-grades': return <InputGrades courses={courses} gradingConfigs={gradingConfigs} studentGrades={studentGrades} onSaveStudent={(id, data) => saveData('student_grades', id, data)} onDeleteStudent={(id) => deleteData('student_grades', id)} activeModel={activeInputModelSubTab} setActiveModel={setActiveInputModelSubTab} />;
      case 'portofolio': return <PortfolioManager courses={courses} gradingConfigs={gradingConfigs} studentGrades={studentGrades} activeModel={activePortfolioModelSubTab} setActiveModel={setActivePortfolioModelSubTab} />;
      case 'analisis': return <AnalysisManager courses={courses} gradingConfigs={gradingConfigs} studentGrades={studentGrades} activeModel={activeAnalysisSubTab} setActiveModel={setActiveAnalysisSubTab} academicSettings={academicSettings} />;
      case 'print': return <DownloadManager courses={courses} gradingConfigs={gradingConfigs} studentGrades={studentGrades} activeModel={activePrintModelSubTab} setActiveModel={setActivePrintModelSubTab} academicSettings={academicSettings} institutionData={institutionData} profileData={profileData} />;
      case 'backup': return <BackupManager user={user} cpls={cpls} courses={courses} gradingConfigs={gradingConfigs} studentGrades={studentGrades} academicSettings={academicSettings} institutionData={institutionData} profileData={profileData} />;
      default: return null;
    }
  };

  return (
    <div className="flex h-screen bg-slate-50 text-slate-900 overflow-hidden font-sans">
      <aside className="w-80 bg-[#0f172a] border-r border-slate-800/50 flex flex-col shadow-2xl z-20 no-print transition-all duration-300 overflow-hidden">
        <div className="p-8 mb-4">
          <div className="flex items-center gap-4 group">
            <div className="w-12 h-12 bg-white rounded-2xl flex items-center justify-center shadow-sm">
              {profileData?.appLogo ? <img src={profileData.appLogo} className="w-8 h-8 object-contain" alt="Logo" /> : <Layers className="text-blue-600" size={24} />}
            </div>
            <div className="flex flex-col">
              <h1 className="font-black text-2xl text-white tracking-tighter leading-none">SIMOBE</h1>
              <span className="text-[10px] font-bold text-blue-400 uppercase tracking-[0.2em] mt-1">Pro Edition</span>
            </div>
          </div>
        </div>
        <nav className="flex-1 px-4 space-y-2 overflow-y-auto scrollbar-hide py-2">
          <NavItem icon={<LayoutDashboard size={20}/>} label="Dashboard" active={activeTab === 'dashboard'} onClick={() => setActiveTab('dashboard')} />
          <NavItem icon={<Settings size={20}/>} label="Pengaturan" active={activeTab === 'pengaturan'} onClick={() => setActiveTab('pengaturan')} />
          <NavItem icon={<Layers size={20}/>} label="Penilaian MK" active={activeTab === 'assessments'} onClick={() => setActiveTab('assessments')} />
          <NavItem icon={<ClipboardList size={20}/>} label="Input Penilaian" active={activeTab === 'input-grades'} onClick={() => setActiveTab('input-grades')} />
          <NavItem icon={<BookCheck size={20}/>} label="Portofolio Mahasiswa" active={activeTab === 'portofolio'} onClick={() => setActiveTab('portofolio')} />
          <NavItem icon={<Microscope size={20}/>} label="Analisis Capaian" active={activeTab === 'analisis'} onClick={() => setActiveTab('analisis')} />
          <NavItem icon={<FileDown size={20}/>} label="Download Berkas" active={activeTab === 'print'} onClick={() => isPrintEnabled && setActiveTab('print')} disabled={!isPrintEnabled} />
          <NavItem icon={<Database size={20}/>} label="Backup dan Restore" active={activeTab === 'backup'} onClick={() => setActiveTab('backup')} />
        </nav>
        {/* APP VERSION FOOTER */}
        <div className="p-6 mt-auto border-t border-slate-800/50">
          <div className="flex items-center gap-2 text-slate-500 font-bold text-[10px] uppercase tracking-widest">
             <div className="w-2 h-2 rounded-full bg-emerald-500 animate-pulse" />
             SIMOBE v1.24
          </div>
          <div className="text-slate-600 font-bold text-[8px] uppercase tracking-widest mt-1 ml-4">
             Pengembang: RB Digital
          </div>
        </div>
      </aside>
      <main className="flex-1 flex flex-col overflow-hidden bg-[#f8fafc]">
        <header className="bg-white border-b px-8 py-5 flex justify-between items-center z-10 no-print">
          <div className="flex items-center gap-4 font-black text-slate-800 uppercase tracking-tighter">
            {activeTab.replace('-', ' ')}
          </div>
          <button onClick={() => setShowProfileModal(true)} className="flex items-center gap-3 p-1.5 pr-5 bg-slate-50 border rounded-full group transition-all hover:bg-white hover:shadow-md">
            {profileData?.photo ? <img src={profileData.photo} className="w-10 h-10 rounded-full object-cover shadow-sm" alt="Dosen" /> : <div className="w-10 h-10 bg-blue-600 text-white rounded-full flex items-center justify-center"><User size={20} /></div>}
            <div className="text-left hidden md:block"><p className="text-[13px] font-black text-slate-800 leading-none">{profileData?.name || 'Set Identitas'}</p></div>
          </button>
        </header>
        <div className="flex-1 overflow-y-auto p-8 scrollbar-hide print:p-0 print:overflow-visible">
          {renderContent()}
        </div>
      </main>
      {showProfileModal && <ProfileModal profileData={profileData} institutionData={institutionData} onClose={() => setShowProfileModal(false)} onSave={(pData, iData) => { saveData('config', 'profile', pData); saveData('config', 'institution', iData); setShowProfileModal(false); }} />}
    </div>
  );
};

export default App;
