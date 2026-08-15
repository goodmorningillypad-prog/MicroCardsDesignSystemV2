// =============================================================================
// MicroCards — Full Lovable Export
// Created by Christopher Reeves Sr., Caleb Reeves & Christopher Reeves
// Mnemonic Glyphs Studio
// =============================================================================
//
// LOVABLE SETUP:
// 1. Paste this file as your App.tsx
// 2. In Lovable chat: "Install: motion lucide-react recharts"
// 3. Add to global CSS:
//    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');
//
// FIND ALL SPOTS TO EDIT: search for "TODO:"
//
// VIDEO CARDS: On card back, find VideoPlaceholder component.
//   Add the real video URL to the `videoUrl` field in each org in ORGS array.
//
// PHONE FRAME: Remove the outer phone frame wrapper at the bottom of this
//   file when deploying as a real mobile app — render the screen content
//   directly as your root element.
// =============================================================================

import { useState, useRef, useMemo } from "react";
import { motion, AnimatePresence } from "motion/react";
import {
  Home, CreditCard, BarChart2, Settings as SettingsIcon,
  Flame, Star, Zap, Heart, ChevronDown, ChevronUp,
  Bell, Moon, Volume2, Shield, Info, LogOut, ArrowRight,
  BookOpen, Target, User, Lock, ChevronLeft, ChevronRight,
  X, Play, Check, RotateCcw, FlaskConical, Video,
} from "lucide-react";
import { BarChart, Bar, XAxis, ResponsiveContainer, Cell } from "recharts";

// =============================================================================
// TYPES
// =============================================================================

type Screen =
  | "landing" | "about"
  | "welcome" | "tutorial" | "home" | "flashcard"
  | "quiz" | "collection" | "progress" | "settings";
type NavTab = "home" | "cards" | "quiz" | "progress" | "settings";
type SettingItem =
  | { icon: React.ReactNode; label: string; type: "nav"; value?: string }
  | { icon: React.ReactNode; label: string; type: "toggle"; toggled: boolean; onToggle: () => void };

// =============================================================================
// ORGANISM / CARD DATA
// TODO: Replace with your real Supabase fetch.
//       videoUrl: add the CDN URL for each organism's flip-side video.
// =============================================================================

const ORGS = [
  {
    id: "staph",
    name: "Staphylococcus aureus", short: "S. aureus",
    villain: "The Cluster King", category: "Bacteria", difficulty: "Hard",
    gramStain: "Gram-positive cocci in clusters",
    facts: ["Catalase +", "Coagulase +", "Beta / non-hemolytic", "Golden colonies"],
    story: "The Cluster King rules a golden palace surrounded by loyal spherical minions. He wears a crown because he's the #1 cause of skin infections — and he's not afraid to break into your bloodstream.",
    symptoms: ["Skin infections", "Abscesses", "Cellulitis", "Bacteremia", "Endocarditis"],
    treatment: "MSSA: Nafcillin / Oxacillin. MRSA: Vancomycin (drug of choice).",
    virulence: "Protein A, Coagulase, TSST-1, Exfoliatin, Leukocidin.",
    pearls: "Most common osteomyelitis in adults. #1 endocarditis in IV drug users. Food poisoning: preformed toxin, onset 2-6 hrs.",
    hasArt: true, artColor: "#FFB300", artEmoji: "crown",
    progress: 85, mastered: true,
    videoUrl: "", // TODO: "https://your-cdn.com/staph_hero_v1.mp4"
  },
  {
    id: "strep",
    name: "Streptococcus pyogenes", short: "Strep. pyogenes",
    villain: "Chain Choker", category: "Bacteria", difficulty: "Medium",
    gramStain: "Gram-positive cocci in chains",
    facts: ["Catalase -", "Beta-hemolytic (Group A)", "Bacitracin sensitive"],
    story: "Chain Choker binds victims in unbreakable chains of cocci. He attacks the throat and skin — and if untreated, your heart valves.",
    symptoms: ["Strep throat", "Scarlet fever", "Impetigo", "Rheumatic fever"],
    treatment: "Penicillin G (drug of choice). No resistance documented.",
    virulence: "M protein, Streptolysin O/S, Hyaluronidase, SPE erythrogenic toxin.",
    pearls: "ASO titer rises after infection. Post-strep GN and rheumatic fever are immune-mediated, not direct infection.",
    hasArt: true, artColor: "#EF5350", artEmoji: "chain",
    progress: 60, mastered: false,
    videoUrl: "",
  },
  {
    id: "pneumo",
    name: "Streptococcus pneumoniae", short: "S. pneumoniae",
    villain: "The Lancet Phantom", category: "Bacteria", difficulty: "Hard",
    gramStain: "Gram-positive lancet-shaped diplococci",
    facts: ["Catalase -", "Alpha-hemolytic", "Encapsulated", "Optochin sensitive"],
    story: "The Lancet Phantom moves in pairs, his lance-shaped body piercing lung tissue. He's the #1 cause of community-acquired pneumonia.",
    symptoms: ["Community-acquired pneumonia", "Meningitis", "Otitis media", "Sinusitis"],
    treatment: "Amoxicillin or Penicillin G. Vancomycin + ceftriaxone if resistant.",
    virulence: "Polysaccharide capsule, IgA protease, Pneumolysin.",
    pearls: "Rusty sputum = classic. Most common meningitis in adults. Vaccine-preventable.",
    hasArt: false, artColor: "#5C6BC0", artEmoji: "trident",
    progress: 40, mastered: false,
    videoUrl: "", // TODO: "https://your-cdn.com/pneumo_hero_v1.mp4"
  },
  {
    id: "ecoli",
    name: "Escherichia coli", short: "E. coli",
    villain: "The Flagellum Phantom", category: "Bacteria", difficulty: "Medium",
    gramStain: "Gram-negative rod",
    facts: ["Facultative anaerobe", "Lactose fermenter", "MacConkey agar +", "Indole +"],
    story: "The Flagellum Phantom uses whip-like tails to escape any environment. He's the most common UTI cause — and can escape into your bloodstream.",
    symptoms: ["UTI (most common)", "Neonatal meningitis", "Traveler's diarrhea", "Bacteremia"],
    treatment: "Fluoroquinolones or TMP-SMX (UTI). Ceftriaxone (meningitis).",
    virulence: "Type 1 fimbriae, LT/ST enterotoxins, Shiga-like toxin (EHEC), K1 capsule.",
    pearls: "K1 capsule causes neonatal meningitis. EHEC O157:H7 causes HUS. Never give antibiotics to EHEC.",
    hasArt: true, artColor: "#43A047", artEmoji: "leaf",
    progress: 72, mastered: false,
    videoUrl: "", // TODO: "https://your-cdn.com/ecoli_hero_v1.mp4"
  },
  {
    id: "mening",
    name: "Neisseria meningitidis", short: "N. meningitidis",
    villain: "The Kidney Baron", category: "Bacteria", difficulty: "Hard",
    gramStain: "Gram-negative diplococci (kidney-bean shaped)",
    facts: ["Oxidase +", "Polysaccharide capsule", "IgA protease", "Maltose fermenter"],
    story: "The Kidney Baron lives in your nasopharynx, biding his time. When he strikes, he crosses the blood-brain barrier with devastating speed.",
    symptoms: ["Bacterial meningitis", "Meningococcemia", "Petechiae/purpura", "Waterhouse-Friderichsen syndrome"],
    treatment: "Penicillin G or Ceftriaxone. Prophylaxis: Rifampin or Ciprofloxacin.",
    virulence: "Capsule, LPS endotoxin, IgA protease, Pili.",
    pearls: "Most common meningitis in teens/young adults. Non-blanching rash = emergency.",
    hasArt: false, artColor: "#1565C0", artEmoji: "bean",
    progress: 20, mastered: false,
    videoUrl: "",
  },
  {
    id: "pseudo",
    name: "Pseudomonas aeruginosa", short: "Pseudomonas",
    villain: "The Blue Phantom", category: "Bacteria", difficulty: "Hard",
    gramStain: "Gram-negative rod",
    facts: ["Blue-green pyocyanin pigment", "Grape-like odor", "Oxidase +", "Non-lactose fermenter"],
    story: "The Blue Phantom leaves a trail of blue-green slime and smells of overripe grapes. He thrives in burn wounds and CF lungs.",
    symptoms: ["Burn wound infections", "CF pneumonia", "Hot tub folliculitis", "Otitis externa"],
    treatment: "Anti-pseudomonal penicillin (Pip-Tazo) + aminoglycoside. Ciprofloxacin (mild).",
    virulence: "Exotoxin A, Alginate biofilm, Pyocyanin, Endotoxin.",
    pearls: "Hot tub folliculitis + swimmer's ear + blue-green pus = Pseudomonas. Ecthyma gangrenosum in neutropenic patients.",
    hasArt: false, artColor: "#00897B", artEmoji: "flask",
    progress: 15, mastered: false,
    videoUrl: "",
  },
  {
    id: "tb",
    name: "Mycobacterium tuberculosis", short: "M. tuberculosis",
    villain: "The Waxy Phantom", category: "Bacteria", difficulty: "Hard",
    gramStain: "Does not Gram stain. Acid-fast (Ziehl-Neelsen stain).",
    facts: ["Acid-fast (mycolic acid wall)", "Cord factor", "Slow grower (weeks)", "Obligate aerobe"],
    story: "The Waxy Phantom cannot be Gram stained. His thick mycolic acid coat repels it. He hides in macrophages for decades.",
    symptoms: ["Night sweats", "Hemoptysis", "Weight loss", "Upper lobe cavitation", "Ghon complex"],
    treatment: "RIPE x 2 months, then RI x 4 months. (Rifampin, Isoniazid, Pyrazinamide, Ethambutol).",
    virulence: "Mycolic acid wall, Cord factor, Sulfatides.",
    pearls: "Upper lobe predilection = high O2. PPD+ = exposure, not active disease. Caseating granulomas on biopsy.",
    hasArt: false, artColor: "#B71C1C", artEmoji: "brick",
    progress: 10, mastered: false,
    videoUrl: "", // TODO: "https://your-cdn.com/tb_hero_v1.mp4"
  },
  {
    id: "tetani",
    name: "Clostridium tetani", short: "C. tetani",
    villain: "The Lockjaw General", category: "Bacteria", difficulty: "Medium",
    gramStain: "Gram-positive rod, obligate anaerobe",
    facts: ["Spore-forming", "Tetanospasmin toxin", "Obligate anaerobe", "Drum-stick spores"],
    story: "The Lockjaw General attacks through dirty wounds. His tetanospasmin toxin travels up motor neurons and locks every muscle in spasm.",
    symptoms: ["Trismus (lockjaw)", "Opisthotonos", "Risus sardonicus", "Autonomic instability"],
    treatment: "TIG (antitoxin) + Metronidazole + wound debridement + Diazepam.",
    virulence: "Tetanospasmin: blocks glycine/GABA release causing spastic paralysis.",
    pearls: "Blocks INHIBITORY neurons (opposite of Botulinum). Vaccination: TDaP.",
    hasArt: false, artColor: "#4E342E", artEmoji: "lock",
    progress: 0, mastered: false,
    videoUrl: "",
  },
  {
    id: "hpylori",
    name: "Helicobacter pylori", short: "H. pylori",
    villain: "The Ulcer King", category: "Bacteria", difficulty: "Medium",
    gramStain: "Gram-negative spiral rod",
    facts: ["Urease + (key test)", "Oxidase +", "Microaerophilic", "Flagellated"],
    story: "The Ulcer King uses his urease enzyme to neutralize stomach acid, creating a safe haven in the mucosa, then drills ulcers into your stomach wall.",
    symptoms: ["Peptic ulcers (duodenal > gastric)", "Gastritis", "MALT lymphoma", "Dyspepsia"],
    treatment: "Triple therapy: PPI + Amoxicillin + Clarithromycin x 14 days.",
    virulence: "Urease, CagA toxin, VacA vacuolating toxin.",
    pearls: "Most common cause of duodenal ulcer. Urea breath test = active infection. Eradication reduces MALT lymphoma risk.",
    hasArt: false, artColor: "#E65100", artEmoji: "spiral",
    progress: 0, mastered: false,
    videoUrl: "",
  },
  {
    id: "tpallidum",
    name: "Treponema pallidum", short: "T. pallidum",
    villain: "The Invisible One", category: "Bacteria", difficulty: "Hard",
    gramStain: "Cannot Gram stain. Too thin. Use darkfield microscopy.",
    facts: ["Spirochete", "Cannot culture", "Darkfield microscopy", "RPR/VDRL then FTA-ABS confirm"],
    story: "The Invisible One cannot be seen with Gram stain. Too thin, no conventional cell wall. He moves in corkscrew spirals and causes syphilis across three insidious stages.",
    symptoms: ["Stage 1: Painless chancre", "Stage 2: Rash on palms/soles, condylomata lata", "Stage 3: Gummas, aortitis, neurosyphilis"],
    treatment: "Penicillin G (all stages). Doxycycline if penicillin-allergic.",
    virulence: "Outer membrane proteins, Endoflagella, Immune evasion.",
    pearls: "Primary = painless ulcer. Secondary = rash everywhere including palms/soles. VDRL false positives: SLE, pregnancy, mono.",
    hasArt: false, artColor: "#546E7A", artEmoji: "ghost",
    progress: 0, mastered: false,
    videoUrl: "", // TODO: "https://your-cdn.com/treponema_hero_v1.mp4"
  },
  {
    id: "cdiff",
    name: "Clostridioides difficile", short: "C. difficile",
    villain: "Clostri the Destroyer", category: "Bacteria", difficulty: "Medium",
    gramStain: "Gram-positive rod, obligate anaerobe",
    facts: ["Spore-forming", "Toxins A + B", "Post-antibiotic colitis", "EIA / PCR for toxin"],
    story: "Clostri the Destroyer waits for you to take antibiotics, then takes over the colon. Toxins A and B destroy the gut lining causing pseudomembranous colitis.",
    symptoms: ["Watery diarrhea (3+ stools/day)", "Pseudomembranous colitis", "Fever", "Leukocytosis"],
    treatment: "Fidaxomicin (preferred) or Vancomycin PO. Stop offending antibiotic. FMT for recurrent.",
    virulence: "Toxin A (enterotoxin), Toxin B (cytotoxin) disrupts actin cytoskeleton.",
    pearls: "Classic: post-antibiotic diarrhea, especially after clindamycin or fluoroquinolones. Spores survive alcohol gels. Use soap + water.",
    hasArt: false, artColor: "#6D4C41", artEmoji: "skull",
    progress: 0, mastered: false,
    videoUrl: "",
  },
  {
    id: "listeria",
    name: "Listeria monocytogenes", short: "Listeria",
    villain: "The Cold Creeper", category: "Bacteria", difficulty: "Medium",
    gramStain: "Gram-positive rod",
    facts: ["Grows at 4 degrees C (refrigerator temp)", "Tumbling motility", "Intracellular pathogen", "Actin rocket propulsion"],
    story: "The Cold Creeper grows in your refrigerator. He moves through cells by shooting actin rockets and targets pregnant women, neonates, and the immunocompromised.",
    symptoms: ["Neonatal meningitis/sepsis", "Meningitis (immunocompromised/elderly)", "Granulomatosis infantiseptica"],
    treatment: "Ampicillin (drug of choice). TMP-SMX if penicillin-allergic.",
    virulence: "Listeriolysin O, ActA (actin polymerization), InlA/InlB invasins.",
    pearls: "Found in deli meats, soft cheese, unpasteurized milk. Second most common neonatal meningitis. Use Ampicillin, not cephalosporins.",
    hasArt: false, artColor: "#0288D1", artEmoji: "ice",
    progress: 0, mastered: false,
    videoUrl: "",
  },
  {
    id: "anthracis",
    name: "Bacillus anthracis", short: "B. anthracis",
    villain: "The Anthrax Lord", category: "Bacteria", difficulty: "Hard",
    gramStain: "Gram-positive rod, obligate aerobe",
    facts: ["Spore-forming", "D-glutamate capsule", "Anthrax toxin (LF + EF + PA)", "Medusa head colonies"],
    story: "The Anthrax Lord forms indestructible spores that survive for decades. His three-part anthrax toxin destroys macrophages and causes massive edema.",
    symptoms: ["Cutaneous: painless eschar", "Inhalation: widened mediastinum", "GI: bloody diarrhea"],
    treatment: "Ciprofloxacin or Doxycycline. Antitoxin for systemic disease.",
    virulence: "PA (protective antigen) + LF (lethal factor) + EF (edema factor).",
    pearls: "Bioterrorism agent. Widened mediastinum on CXR = inhalation anthrax until proven otherwise.",
    hasArt: false, artColor: "#212121", artEmoji: "danger",
    progress: 0, mastered: false,
    videoUrl: "",
  },
  {
    id: "mrsa",
    name: "Methicillin-resistant S. aureus", short: "MRSA",
    villain: "The Resistant", category: "Bacteria", difficulty: "Hard",
    gramStain: "Gram-positive cocci in clusters",
    facts: ["mecA gene altered PBP2a", "Vancomycin-sensitive", "No beta-lactams effective"],
    story: "The Resistant wears a shield forged from altered PBP2a. Beta-lactam antibiotics bounce off harmlessly. Only Vancomycin can pierce his armor.",
    symptoms: ["Skin/soft tissue infections (CA-MRSA)", "Pneumonia", "Bacteremia", "Endocarditis"],
    treatment: "Vancomycin (IV). Alternatives: Daptomycin, Linezolid, Ceftaroline.",
    virulence: "PBP2a (mecA gene), Panton-Valentine Leukocidin (CA-MRSA).",
    pearls: "CA-MRSA causes skin infections. HA-MRSA causes pneumonia and bacteremia. Daptomycin is inactivated by surfactant so do NOT use for pneumonia.",
    hasArt: true, artColor: "#455A64", artEmoji: "shield",
    progress: 45, mastered: false,
    videoUrl: "",
  },
  {
    id: "candida",
    name: "Candida albicans", short: "C. albicans",
    villain: "The White Witch", category: "Fungi", difficulty: "Medium",
    gramStain: "Gram-positive fungus (budding yeast + pseudohyphae)",
    facts: ["Germ tube + (at 37 degrees C)", "Pseudohyphae", "KOH prep", "Chlamydospores"],
    story: "The White Witch transforms between yeast and pseudohyphae. She causes thrush in the immunocompromised and diaper rash in infants.",
    symptoms: ["Oral thrush", "Vaginal candidiasis", "Esophagitis", "Candidemia (immunocompromised)"],
    treatment: "Fluconazole (mucocutaneous). Echinocandins or Amphotericin B (invasive).",
    virulence: "Als adhesins, Candidalysin, Biofilm formation, Phenotypic switching.",
    pearls: "Germ tube = rapid ID test. Risk factors: antibiotics, steroids, HIV, diabetes. Esophagitis causes dysphagia in AIDS.",
    hasArt: true, artColor: "#AB47BC", artEmoji: "flower",
    progress: 30, mastered: false,
    videoUrl: "",
  },
  {
    id: "flu",
    name: "Influenza A virus", short: "Influenza A",
    villain: "Flu Phantom", category: "Viruses", difficulty: "Easy",
    gramStain: "Virus. Not stained.",
    facts: ["HA + NA surface proteins", "Antigenic drift + shift", "Negative-sense RNA", "Segmented genome"],
    story: "The Flu Phantom changes his disguise each season through antigenic drift. Every decade he pulls off an antigenic shift and causes global pandemics.",
    symptoms: ["Abrupt fever + myalgias", "Headache + cough", "Secondary bacterial pneumonia"],
    treatment: "Oseltamivir (Tamiflu) within 48 hours. Annual vaccination.",
    virulence: "HA (hemagglutinin, cell entry), NA (neuraminidase, viral release), M2 ion channel.",
    pearls: "Drift = gradual (seasonal epidemics). Shift = abrupt reassortment (pandemics). Amantadine works only on Influenza A.",
    hasArt: true, artColor: "#1E88E5", artEmoji: "snowflake",
    progress: 0, mastered: false, locked: true,
    videoUrl: "",
  },
] as const;

type OrgId = typeof ORGS[number]["id"];

// =============================================================================
// QUIZ QUESTIONS
// TODO: Replace with Supabase fetch. Each question links to an orgId.
// =============================================================================

const QUESTIONS = [
  {
    id: "q1", orgId: "staph" as OrgId,
    question: "Catalase and coagulase — what results for S. aureus?",
    options: [{ id: "A", text: "Catalase + / Coagulase -" }, { id: "B", text: "Both positive" }, { id: "C", text: "Catalase - / Coagulase +" }, { id: "D", text: "Both negative" }],
    correct: "B",
  },
  {
    id: "q2", orgId: "strep" as OrgId,
    question: "Catalase result for Strep pyogenes?",
    options: [{ id: "A", text: "Positive" }, { id: "B", text: "Beta only" }, { id: "C", text: "Negative" }, { id: "D", text: "Variable" }],
    correct: "C",
  },
  {
    id: "q3", orgId: "pneumo" as OrgId,
    question: "Cell arrangement of S. pneumoniae under the microscope?",
    options: [{ id: "A", text: "Cocci in clusters" }, { id: "B", text: "Lancet-shaped diplococci (pairs)" }, { id: "C", text: "Cocci in chains" }, { id: "D", text: "Gram-negative rod" }],
    correct: "B",
  },
  {
    id: "q4", orgId: "ecoli" as OrgId,
    question: "Gram stain and shape of E. coli?",
    options: [{ id: "A", text: "Gram-positive coccus" }, { id: "B", text: "Gram-positive rod" }, { id: "C", text: "Gram-negative coccus" }, { id: "D", text: "Gram-negative rod" }],
    correct: "D",
  },
  {
    id: "q5", orgId: "mening" as OrgId,
    question: "Gram stain and shape of N. meningitidis?",
    options: [{ id: "A", text: "Gram-negative diplococci (kidney-bean)" }, { id: "B", text: "Gram-positive cocci in chains" }, { id: "C", text: "Gram-positive diplococci (lancet)" }, { id: "D", text: "Gram-negative rod" }],
    correct: "A",
  },
  {
    id: "q6", orgId: "pseudo" as OrgId,
    question: "What pigment and smell are diagnostic clues for Pseudomonas?",
    options: [{ id: "A", text: "Red pigment, sweet smell" }, { id: "B", text: "Blue-green pyocyanin pigment, grape-like smell" }, { id: "C", text: "Yellow pigment, ammonia smell" }, { id: "D", text: "No pigment, odorless" }],
    correct: "B",
  },
  {
    id: "q7", orgId: "tb" as OrgId,
    question: "What stain is needed since Gram stain won't work on TB?",
    options: [{ id: "A", text: "Gram stain" }, { id: "B", text: "Giemsa stain" }, { id: "C", text: "Ziehl-Neelsen (acid-fast) stain" }, { id: "D", text: "India ink stain" }],
    correct: "C",
  },
  {
    id: "q8", orgId: "tetani" as OrgId,
    question: "Gram stain and oxygen requirement for C. tetani?",
    options: [{ id: "A", text: "Gram-negative, facultative anaerobe" }, { id: "B", text: "Gram-positive, obligate anaerobe" }, { id: "C", text: "Gram-negative, obligate anaerobe" }, { id: "D", text: "Gram-positive, obligate aerobe" }],
    correct: "B",
  },
  {
    id: "q9", orgId: "hpylori" as OrgId,
    question: "What enzyme neutralizes stomach acid locally for H. pylori?",
    options: [{ id: "A", text: "Urease" }, { id: "B", text: "Coagulase" }, { id: "C", text: "Catalase" }, { id: "D", text: "Protease" }],
    correct: "A",
  },
  {
    id: "q10", orgId: "tpallidum" as OrgId,
    question: "Why can't you Gram stain Treponema pallidum?",
    options: [{ id: "A", text: "It stains Gram-negative" }, { id: "B", text: "It stains Gram-positive" }, { id: "C", text: "It dissolves during staining" }, { id: "D", text: "Too thin / lacks a conventional cell wall visible on Gram stain" }],
    correct: "D",
  },
  {
    id: "q11", orgId: "cdiff" as OrgId,
    question: "Gram stain, shape, and oxygen requirement of C. difficile?",
    options: [{ id: "A", text: "Gram-negative rod, facultative anaerobe" }, { id: "B", text: "Gram-negative rod, obligate anaerobe" }, { id: "C", text: "Gram-positive rod, obligate anaerobe" }, { id: "D", text: "Gram-positive rod, obligate aerobe" }],
    correct: "C",
  },
  {
    id: "q12", orgId: "listeria" as OrgId,
    question: "Gram stain, shape, and unusual growth trait of Listeria?",
    options: [{ id: "A", text: "Gram-positive rod, grows in cold temperatures" }, { id: "B", text: "Gram-negative rod, grows in cold temperatures" }, { id: "C", text: "Gram-positive rod, strictly warm-growing" }, { id: "D", text: "Gram-positive coccus, heat-tolerant" }],
    correct: "A",
  },
  {
    id: "q13", orgId: "anthracis" as OrgId,
    question: "Gram stain, shape, and oxygen requirement of B. anthracis?",
    options: [{ id: "A", text: "Gram-positive rod, obligate aerobe" }, { id: "B", text: "Gram-positive rod, obligate anaerobe" }, { id: "C", text: "Gram-negative rod, obligate aerobe" }, { id: "D", text: "Gram-positive coccus, facultative anaerobe" }],
    correct: "A",
  },
] as const;

// =============================================================================
// STATIC DATA
// =============================================================================

const WEEKLY = [
  { day: "M", v: 12 }, { day: "T", v: 28 }, { day: "W", v: 8 },
  { day: "T", v: 35 }, { day: "F", v: 22 }, { day: "S", v: 40 }, { day: "S", v: 16 },
];

const CONFETTI = Array.from({ length: 18 }, (_, i) => ({
  id: i, x: 8 + (i * 5.2) % 84, delay: (i * 0.055) % 0.65,
  color: ["#FFD54A", "#A3E635", "#FF6B6B", "#42A5F5", "#CE93D8"][i % 5],
  size: 5 + (i % 3) * 3,
}));

// =============================================================================
// SVG VILLAIN FACES
// Replace any of these with Caleb's Grok art by swapping the SVG body with:
//   <img src="YOUR_URL" alt={orgName} className="w-full h-full object-contain" />
// =============================================================================

function StaphFace({ size = 160 }: { size?: number }) {
  return (
    <svg width={size} height={size} viewBox="0 0 200 200" fill="none">
      <circle cx="46" cy="74" r="16" fill="#FFB300" stroke="#E65100" strokeWidth="2.5" />
      <circle cx="154" cy="74" r="16" fill="#FFB300" stroke="#E65100" strokeWidth="2.5" />
      <circle cx="100" cy="36" r="16" fill="#FFB300" stroke="#E65100" strokeWidth="2.5" />
      <circle cx="38" cy="134" r="12" fill="#FFB300" stroke="#E65100" strokeWidth="2" />
      <circle cx="162" cy="134" r="12" fill="#FFB300" stroke="#E65100" strokeWidth="2" />
      <circle cx="100" cy="112" r="74" fill="#FFD54A" stroke="#E65100" strokeWidth="3.5" />
      <path d="M72 68 L79 50 L100 63 L121 50 L128 68 L115 61 L100 71 L85 61Z" fill="#FFB300" stroke="#E65100" strokeWidth="2.5" />
      <ellipse cx="100" cy="118" rx="52" ry="46" fill="#FFC107" opacity="0.28" />
      <ellipse cx="81" cy="104" rx="11" ry="13" fill="#111" />
      <ellipse cx="119" cy="104" rx="11" ry="13" fill="#111" />
      <circle cx="85" cy="100" r="4" fill="white" />
      <circle cx="123" cy="100" r="4" fill="white" />
      <path d="M74 134 Q100 157 126 134" stroke="#E65100" strokeWidth="3.5" fill="none" strokeLinecap="round" />
      <path d="M78 138 Q100 158 122 138" fill="#FF8F00" />
      <rect x="86" y="135" width="10" height="11" rx="2" fill="white" />
      <rect x="99" y="135" width="10" height="11" rx="2" fill="white" />
      <rect x="112" y="135" width="10" height="11" rx="2" fill="white" />
    </svg>
  );
}

function EcoliFace({ size = 160 }: { size?: number }) {
  return (
    <svg width={size} height={size} viewBox="0 0 200 200" fill="none">
      <path d="M34 86 Q10 62 19 37 Q30 13 53 23" stroke="#2E7D32" strokeWidth="3.5" fill="none" strokeLinecap="round" />
      <path d="M34 136 Q7 150 12 177 Q19 200 48 193" stroke="#2E7D32" strokeWidth="3.5" fill="none" strokeLinecap="round" />
      <path d="M166 86 Q190 62 181 37 Q170 13 147 23" stroke="#2E7D32" strokeWidth="3.5" fill="none" strokeLinecap="round" />
      <path d="M166 136 Q193 150 188 177 Q181 200 152 193" stroke="#2E7D32" strokeWidth="3.5" fill="none" strokeLinecap="round" />
      <rect x="30" y="58" width="140" height="104" rx="52" fill="#66BB6A" stroke="#2E7D32" strokeWidth="3.5" />
      <line x1="75" y1="58" x2="75" y2="162" stroke="#388E3C" strokeWidth="2.5" opacity="0.3" />
      <line x1="100" y1="58" x2="100" y2="162" stroke="#388E3C" strokeWidth="2.5" opacity="0.3" />
      <line x1="125" y1="58" x2="125" y2="162" stroke="#388E3C" strokeWidth="2.5" opacity="0.3" />
      <ellipse cx="100" cy="110" rx="52" ry="38" fill="#81C784" opacity="0.38" />
      <ellipse cx="82" cy="102" rx="11" ry="12" fill="#111" />
      <ellipse cx="118" cy="102" rx="11" ry="12" fill="#111" />
      <circle cx="86" cy="98" r="3.5" fill="white" />
      <circle cx="122" cy="98" r="3.5" fill="white" />
      <path d="M80 128 Q100 142 120 124" stroke="#2E7D32" strokeWidth="3" fill="none" strokeLinecap="round" />
    </svg>
  );
}

function StrepFace({ size = 160 }: { size?: number }) {
  const chain: [number, number][] = [[100, 28], [76, 42], [58, 60], [52, 84], [124, 42], [142, 60], [148, 84]];
  return (
    <svg width={size} height={size} viewBox="0 0 200 210" fill="none">
      {chain.map(([cx, cy], i) => (
        <circle key={i} cx={cx} cy={cy} r="12" fill="#EF5350" stroke="#B71C1C" strokeWidth="2.5" />
      ))}
      <circle cx="100" cy="130" r="74" fill="#EF5350" stroke="#B71C1C" strokeWidth="3.5" />
      <ellipse cx="100" cy="135" rx="52" ry="48" fill="#E53935" opacity="0.28" />
      <path d="M68 103 L90 113" stroke="#B71C1C" strokeWidth="4" strokeLinecap="round" />
      <path d="M132 103 L110 113" stroke="#B71C1C" strokeWidth="4" strokeLinecap="round" />
      <ellipse cx="82" cy="122" rx="11" ry="12" fill="#111" />
      <ellipse cx="118" cy="122" rx="11" ry="12" fill="#111" />
      <circle cx="86" cy="118" r="3.5" fill="white" />
      <circle cx="122" cy="118" r="3.5" fill="white" />
      <path d="M76 152 Q100 142 124 152" stroke="#B71C1C" strokeWidth="3.5" fill="none" strokeLinecap="round" />
      <path d="M68 166 Q100 182 132 166" stroke="#7F0000" strokeWidth="3" fill="none" strokeLinecap="round" strokeDasharray="8 5" />
    </svg>
  );
}

function MRSAFace({ size = 160 }: { size?: number }) {
  return (
    <svg width={size} height={size} viewBox="0 0 200 200" fill="none">
      <circle cx="38" cy="100" r="16" fill="#455A64" stroke="#263238" strokeWidth="2.5" />
      <circle cx="162" cy="100" r="16" fill="#455A64" stroke="#263238" strokeWidth="2.5" />
      <circle cx="100" cy="38" r="16" fill="#455A64" stroke="#263238" strokeWidth="2.5" />
      <circle cx="100" cy="162" r="16" fill="#455A64" stroke="#263238" strokeWidth="2.5" />
      <circle cx="100" cy="100" r="64" fill="#546E7A" stroke="#263238" strokeWidth="3.5" />
      <ellipse cx="100" cy="105" rx="48" ry="42" fill="#607D8B" opacity="0.34" />
      <path d="M100 62 L118 72 L118 90 Q118 108 100 116 Q82 108 82 90 L82 72Z" fill="#263238" stroke="#78909C" strokeWidth="1.5" />
      <text x="100" y="100" textAnchor="middle" fill="#F44336" fontSize="18" fontWeight="900" fontFamily="monospace">R</text>
      <ellipse cx="82" cy="130" rx="11" ry="12" fill="#111" />
      <ellipse cx="118" cy="130" rx="11" ry="12" fill="#111" />
      <ellipse cx="82" cy="130" rx="5.5" ry="6.5" fill="#F44336" />
      <ellipse cx="118" cy="130" rx="5.5" ry="6.5" fill="#F44336" />
      <circle cx="83" cy="127" r="2.5" fill="white" opacity="0.55" />
      <circle cx="119" cy="127" r="2.5" fill="white" opacity="0.55" />
      <path d="M76 154 L124 154" stroke="#263238" strokeWidth="3.5" strokeLinecap="round" />
      <line x1="84" y1="154" x2="84" y2="165" stroke="#90A4AE" strokeWidth="2.5" strokeLinecap="round" />
      <line x1="94" y1="154" x2="94" y2="165" stroke="#90A4AE" strokeWidth="2.5" strokeLinecap="round" />
      <line x1="106" y1="154" x2="106" y2="165" stroke="#90A4AE" strokeWidth="2.5" strokeLinecap="round" />
      <line x1="116" y1="154" x2="116" y2="165" stroke="#90A4AE" strokeWidth="2.5" strokeLinecap="round" />
    </svg>
  );
}

function CandidaFace({ size = 160 }: { size?: number }) {
  return (
    <svg width={size} height={size} viewBox="0 0 200 200" fill="none">
      <path d="M100 46 Q80 20 56 16 Q36 13 28 28" stroke="#9C27B0" strokeWidth="3" fill="none" strokeLinecap="round" />
      <path d="M100 46 Q120 20 144 16 Q164 13 172 28" stroke="#9C27B0" strokeWidth="3" fill="none" strokeLinecap="round" />
      <path d="M156 120 Q184 113 194 135 Q202 157 186 170" stroke="#9C27B0" strokeWidth="3" fill="none" strokeLinecap="round" />
      <path d="M44 120 Q16 113 6 135 Q-2 157 14 170" stroke="#9C27B0" strokeWidth="3" fill="none" strokeLinecap="round" />
      <circle cx="100" cy="118" r="72" fill="#CE93D8" stroke="#7B1FA2" strokeWidth="3.5" />
      <ellipse cx="100" cy="123" rx="50" ry="44" fill="#E1BEE7" opacity="0.42" />
      <path d="M72 62 L80 44 L92 56 L100 40 L108 56 L120 44 L128 62 L115 56 L100 65 L85 56Z" fill="#AB47BC" stroke="#7B1FA2" strokeWidth="2.5" />
      <ellipse cx="82" cy="112" rx="11" ry="12" fill="#111" />
      <ellipse cx="118" cy="112" rx="11" ry="12" fill="#111" />
      <ellipse cx="82" cy="112" rx="5.5" ry="6" fill="#CE93D8" />
      <ellipse cx="118" cy="112" rx="5.5" ry="6" fill="#CE93D8" />
      <circle cx="83" cy="109" r="3" fill="white" />
      <circle cx="119" cy="109" r="3" fill="white" />
      <path d="M78 138 Q100 153 122 138" stroke="#7B1FA2" strokeWidth="3" fill="none" strokeLinecap="round" />
    </svg>
  );
}

function InfluenzaFace({ size = 160 }: { size?: number }) {
  const spikes = [0, 30, 60, 90, 120, 150, 180, 210, 240, 270, 300, 330];
  const tips = [0, 45, 90, 135, 180, 225, 270, 315];
  return (
    <svg width={size} height={size} viewBox="0 0 200 200" fill="none">
      {spikes.map((a) => {
        const r = (a * Math.PI) / 180;
        return <line key={a} x1={100 + Math.cos(r) * 64} y1={100 + Math.sin(r) * 64} x2={100 + Math.cos(r) * 88} y2={100 + Math.sin(r) * 88} stroke="#1565C0" strokeWidth="3.5" strokeLinecap="round" />;
      })}
      {tips.map((a) => {
        const r = (a * Math.PI) / 180;
        return <circle key={a} cx={100 + Math.cos(r) * 90} cy={100 + Math.sin(r) * 90} r="5.5" fill="#1E88E5" stroke="#1565C0" strokeWidth="2" />;
      })}
      <circle cx="100" cy="100" r="64" fill="#42A5F5" stroke="#1565C0" strokeWidth="3.5" />
      <ellipse cx="100" cy="105" rx="46" ry="40" fill="#64B5F6" opacity="0.38" />
      <ellipse cx="82" cy="94" rx="11" ry="12" fill="#111" />
      <ellipse cx="118" cy="94" rx="11" ry="12" fill="#111" />
      <circle cx="86" cy="90" r="3.5" fill="white" />
      <circle cx="122" cy="90" r="3.5" fill="white" />
      <path d="M74 118 Q86 130 100 118 Q114 130 126 118" stroke="#1565C0" strokeWidth="3.5" fill="none" strokeLinecap="round" />
    </svg>
  );
}

// Placeholder for organisms without Grok art yet
function PlaceholderArt({ color, size = 160, name }: { color: string; size?: number; name: string }) {
  const s = size;
  const initials = name.split(" ").map(w => w[0]).join("").slice(0, 2).toUpperCase();
  return (
    <svg width={s} height={s} viewBox={`0 0 ${s} ${s}`} fill="none">
      <circle cx={s / 2} cy={s / 2} r={s / 2 - 6} fill={color + "15"} stroke={color + "40"} strokeWidth="2.5" strokeDasharray="10 5" />
      <circle cx={s / 2} cy={s / 2} r={s / 3} fill={color + "20"} />
      <text x={s / 2} y={s / 2 + s * 0.1} textAnchor="middle" fontSize={s * 0.22} fontFamily="system-ui" fontWeight="700" fill={color + "99"}>{initials}</text>
      <text x={s / 2} y={s / 2 + s * 0.36} textAnchor="middle" fontSize={s * 0.065} fill="#B0B0B0" fontFamily="system-ui" fontWeight="600" letterSpacing="0.06em">ART COMING SOON</text>
    </svg>
  );
}

function SrCat({ size = 160 }: { size?: number }) {
  return (
    <svg width={size} height={size} viewBox="0 0 200 220" fill="none">
      <rect x="32" y="148" width="136" height="72" rx="18" fill="#2C3E50" />
      <polygon points="100,148 82,148 90,190 100,178" fill="#1A252F" />
      <polygon points="100,148 118,148 110,190 100,178" fill="#1A252F" />
      <rect x="88" y="148" width="24" height="48" rx="4" fill="#ECF0F1" />
      <polygon points="100,154 96,168 100,194 104,168" fill="#E74C3C" />
      <path d="M68 158 Q54 162 50 176 Q48 188 58 192" stroke="#BDC3C7" strokeWidth="3.5" fill="none" strokeLinecap="round"/>
      <circle cx="58" cy="195" r="6" fill="none" stroke="#BDC3C7" strokeWidth="3"/>
      <path d="M68 158 Q72 152 78 155" stroke="#BDC3C7" strokeWidth="3.5" fill="none" strokeLinecap="round"/>
      <circle cx="78" cy="157" r="4" fill="#BDC3C7"/>
      <ellipse cx="100" cy="100" rx="62" ry="60" fill="#8D9DB6" />
      <polygon points="50,58 38,22 72,48" fill="#8D9DB6" />
      <polygon points="150,58 162,22 128,48" fill="#8D9DB6" />
      <polygon points="52,56 44,30 68,50" fill="#C9A8B8" opacity="0.6"/>
      <polygon points="148,56 156,30 132,50" fill="#C9A8B8" opacity="0.6"/>
      <ellipse cx="100" cy="108" rx="44" ry="38" fill="#A0AEC0" opacity="0.4"/>
      <ellipse cx="82" cy="92" rx="10" ry="11" fill="#2D3748" />
      <ellipse cx="118" cy="92" rx="10" ry="11" fill="#2D3748" />
      <circle cx="86" cy="88" r="4" fill="white" />
      <circle cx="122" cy="88" r="4" fill="white" />
      <path d="M72 80 Q82 75 92 80" stroke="#4A5568" strokeWidth="3" fill="none" strokeLinecap="round"/>
      <path d="M108 80 Q118 75 128 80" stroke="#4A5568" strokeWidth="3" fill="none" strokeLinecap="round"/>
      <ellipse cx="100" cy="106" rx="5" ry="3.5" fill="#718096"/>
      <ellipse cx="100" cy="130" rx="32" ry="20" fill="#E2E8F0" />
      <ellipse cx="100" cy="128" rx="28" ry="16" fill="#EDF2F7" />
      <path d="M82 120 Q84 134 86 138" stroke="#CBD5E0" strokeWidth="1.5" fill="none"/>
      <path d="M92 118 Q94 134 95 140" stroke="#CBD5E0" strokeWidth="1.5" fill="none"/>
      <path d="M100 117 Q100 134 100 141" stroke="#CBD5E0" strokeWidth="1.5" fill="none"/>
      <path d="M108 118 Q106 134 105 140" stroke="#CBD5E0" strokeWidth="1.5" fill="none"/>
      <path d="M118 120 Q116 134 114 138" stroke="#CBD5E0" strokeWidth="1.5" fill="none"/>
      <line x1="55" y1="110" x2="90" y2="112" stroke="#CBD5E0" strokeWidth="1.5"/>
      <line x1="55" y1="116" x2="90" y2="115" stroke="#CBD5E0" strokeWidth="1.5"/>
      <line x1="145" y1="110" x2="110" y2="112" stroke="#CBD5E0" strokeWidth="1.5"/>
      <line x1="145" y1="116" x2="110" y2="115" stroke="#CBD5E0" strokeWidth="1.5"/>
      <path d="M92 118 Q100 124 108 118" stroke="#718096" strokeWidth="2.5" fill="none" strokeLinecap="round"/>
    </svg>
  );
}

function CalebCat({ size = 160 }: { size?: number }) {
  return (
    <svg width={size} height={size} viewBox="0 0 200 220" fill="none">
      <rect x="36" y="148" width="128" height="72" rx="18" fill="#E67E22" />
      <rect x="36" y="158" width="128" height="6" rx="2" fill="#D35400" opacity="0.4"/>
      <path d="M22 80 Q8 100 22 120" stroke="#8B6914" strokeWidth="4" fill="none" strokeLinecap="round"/>
      <line x1="22" y1="80" x2="22" y2="120" stroke="#6B4F0A" strokeWidth="2" strokeDasharray="3 3"/>
      <line x1="22" y1="100" x2="80" y2="100" stroke="#A0522D" strokeWidth="3" strokeLinecap="round"/>
      <polygon points="80,96 92,100 80,104" fill="#C0392B"/>
      <path d="M22,100 L18,96 M22,100 L18,104" stroke="#8B6914" strokeWidth="2" strokeLinecap="round"/>
      <path d="M56 152 Q40 138 26 108" stroke="#E67E22" strokeWidth="12" strokeLinecap="round" fill="none"/>
      <ellipse cx="106" cy="100" rx="60" ry="58" fill="#E67E22" />
      <polygon points="58,62 46,26 78,52" fill="#E67E22" />
      <polygon points="154,62 166,26 134,52" fill="#E67E22" />
      <polygon points="60,58 50,32 74,50" fill="#FDEBD0" opacity="0.7"/>
      <polygon points="152,58 162,32 138,50" fill="#FDEBD0" opacity="0.7"/>
      <path d="M86 68 Q106 62 126 68" stroke="#D35400" strokeWidth="2.5" fill="none" opacity="0.5"/>
      <path d="M88 74 Q106 68 124 74" stroke="#D35400" strokeWidth="2" fill="none" opacity="0.4"/>
      <ellipse cx="106" cy="108" rx="44" ry="38" fill="#FDEBD0" opacity="0.45"/>
      <ellipse cx="88" cy="92" rx="11" ry="12" fill="#2D3748" />
      <ellipse cx="124" cy="92" rx="11" ry="12" fill="#2D3748" />
      <ellipse cx="88" cy="92" rx="5" ry="6" fill="#27AE60" />
      <ellipse cx="124" cy="92" rx="5" ry="6" fill="#27AE60" />
      <circle cx="91" cy="88" r="4" fill="white" />
      <circle cx="127" cy="88" r="4" fill="white" />
      <path d="M78 79 Q88 73 98 78" stroke="#D35400" strokeWidth="2.5" fill="none" strokeLinecap="round"/>
      <path d="M114 79 Q124 73 134 78" stroke="#D35400" strokeWidth="2.5" fill="none" strokeLinecap="round"/>
      <ellipse cx="106" cy="107" rx="5" ry="3.5" fill="#C0392B"/>
      <path d="M90 118 Q106 132 122 118" stroke="#C0392B" strokeWidth="3" fill="none" strokeLinecap="round"/>
      <line x1="54" y1="108" x2="88" y2="110" stroke="#FAD7A0" strokeWidth="1.5"/>
      <line x1="54" y1="114" x2="88" y2="113" stroke="#FAD7A0" strokeWidth="1.5"/>
      <line x1="158" y1="108" x2="124" y2="110" stroke="#FAD7A0" strokeWidth="1.5"/>
      <line x1="158" y1="114" x2="124" y2="113" stroke="#FAD7A0" strokeWidth="1.5"/>
    </svg>
  );
}

function ChrisCat({ size = 160 }: { size?: number }) {
  return (
    <svg width={size} height={size} viewBox="0 0 200 220" fill="none">
      <rect x="36" y="148" width="128" height="72" rx="18" fill="#5D4037" />
      <rect x="36" y="148" width="128" height="20" rx="12" fill="#6D4C41" />
      <polygon points="100,148 88,148 92,168" fill="#EFEBE9"/>
      <polygon points="100,148 112,148 108,168" fill="#EFEBE9"/>
      <rect x="158" y="20" width="7" height="110" rx="3" fill="#BDC3C7" />
      <rect x="156" y="20" width="11" height="16" rx="2" fill="#95A5A6" />
      <rect x="146" y="108" width="30" height="8" rx="4" fill="#7F8C8D" />
      <rect x="159" y="116" width="5" height="22" rx="2.5" fill="#795548" />
      <line x1="159" y1="120" x2="164" y2="120" stroke="#A1887F" strokeWidth="1.5"/>
      <line x1="159" y1="126" x2="164" y2="126" stroke="#A1887F" strokeWidth="1.5"/>
      <line x1="159" y1="132" x2="164" y2="132" stroke="#A1887F" strokeWidth="1.5"/>
      <path d="M148 152 Q158 130 162 110" stroke="#5D4037" strokeWidth="12" strokeLinecap="round" fill="none"/>
      <ellipse cx="97" cy="100" rx="60" ry="58" fill="#795548" />
      <polygon points="50,62 38,26 72,52" fill="#795548" />
      <polygon points="144,62 156,26 122,52" fill="#795548" />
      <polygon points="52,58 44,32 68,50" fill="#FFCCBC" opacity="0.6"/>
      <polygon points="142,58 152,32 128,50" fill="#FFCCBC" opacity="0.6"/>
      <ellipse cx="97" cy="108" rx="44" ry="38" fill="#BCAAA4" opacity="0.38"/>
      <ellipse cx="80" cy="93" rx="11" ry="10" fill="#1A1A2E" />
      <ellipse cx="114" cy="93" rx="11" ry="10" fill="#1A1A2E" />
      <ellipse cx="80" cy="93" rx="5" ry="5" fill="#6C5CE7" />
      <ellipse cx="114" cy="93" rx="5" ry="5" fill="#6C5CE7" />
      <circle cx="83" cy="89" r="3.5" fill="white" />
      <circle cx="117" cy="89" r="3.5" fill="white" />
      <path d="M70 82 Q80 78 90 82" stroke="#4E342E" strokeWidth="2.5" fill="none" strokeLinecap="round"/>
      <path d="M104 80 Q114 77 124 82" stroke="#4E342E" strokeWidth="2.5" fill="none" strokeLinecap="round"/>
      <ellipse cx="97" cy="107" rx="5" ry="3.5" fill="#6D4C41"/>
      <path d="M86 118 Q97 126 108 120" stroke="#6D4C41" strokeWidth="2.5" fill="none" strokeLinecap="round"/>
      <line x1="46" y1="108" x2="80" y2="110" stroke="#D7CCC8" strokeWidth="1.5"/>
      <line x1="46" y1="114" x2="80" y2="113" stroke="#D7CCC8" strokeWidth="1.5"/>
      <line x1="148" y1="108" x2="114" y2="110" stroke="#D7CCC8" strokeWidth="1.5"/>
      <line x1="148" y1="114" x2="114" y2="113" stroke="#D7CCC8" strokeWidth="1.5"/>
      <rect x="122" y="52" width="4" height="22" rx="2" fill="#FDD835" transform="rotate(-20 122 52)"/>
      <polygon points="122,52 126,52 124,44" fill="#FF7043" transform="rotate(-20 122 52)"/>
    </svg>
  );
}

function renderVillain(id: string, size = 160, name = "") {
  switch (id) {
    case "staph": return <StaphFace size={size} />;
    case "ecoli": return <EcoliFace size={size} />;
    case "strep": return <StrepFace size={size} />;
    case "mrsa": return <MRSAFace size={size} />;
    case "candida": return <CandidaFace size={size} />;
    case "flu": return <InfluenzaFace size={size} />;
    default: {
      const org = ORGS.find(o => o.id === id);
      return org ? <PlaceholderArt color={org.artColor} size={size} name={name || org.name} /> : <StaphFace size={size} />;
    }
  }
}

// =============================================================================
// PROGRESS RING
// =============================================================================

function ProgressRing({ pct, size = 110, sw = 9, color = "#111" }: { pct: number; size?: number; sw?: number; color?: string }) {
  const r = (size - sw) / 2;
  const circ = 2 * Math.PI * r;
  return (
    <svg width={size} height={size}>
      <circle cx={size / 2} cy={size / 2} r={r} fill="none" stroke="#E7E7E7" strokeWidth={sw} />
      <motion.circle
        cx={size / 2} cy={size / 2} r={r} fill="none" stroke={color} strokeWidth={sw} strokeLinecap="round"
        strokeDasharray={circ}
        initial={{ strokeDashoffset: circ }}
        animate={{ strokeDashoffset: circ - (pct / 100) * circ }}
        transition={{ duration: 1.3, ease: "easeOut" }}
        transform={`rotate(-90 ${size / 2} ${size / 2})`}
      />
    </svg>
  );
}

// =============================================================================
// VIDEO PLACEHOLDER
// TODO Caleb: When you have video URLs, add them to each org's `videoUrl` field.
//   The <video> tag is already wired up inside VideoPlaceholder — just needs the URL.
//   To use a custom video player component, replace the <video> tag below.
// =============================================================================

function VideoPlaceholder({ videoUrl, orgName }: { videoUrl: string; orgName: string }) {
  if (videoUrl) {
    return (
      <div className="w-full rounded-2xl overflow-hidden bg-black" style={{ aspectRatio: "9/16" }}>
        <video
          className="w-full h-full object-contain"
          src={videoUrl}
          controls
          playsInline
          // TODO: add poster="YOUR_THUMBNAIL_URL" for a cover image before the video loads
        />
      </div>
    );
  }

  return (
    <div className="w-full rounded-2xl bg-[#111] flex flex-col items-center justify-center gap-3 border border-[#222] relative overflow-hidden" style={{ aspectRatio: "9/16" }}>
      <div className="absolute inset-0 opacity-[0.06]" style={{ backgroundImage: "repeating-linear-gradient(45deg, #fff 0, #fff 1px, transparent 0, transparent 50%)", backgroundSize: "12px 12px" }} />
      <div className="relative z-10 flex flex-col items-center gap-2 text-center px-4">
        <div className="w-12 h-12 rounded-full bg-white/10 flex items-center justify-center">
          <Video className="w-5 h-5 text-white/50" />
        </div>
        <div className="text-white/70 text-[13px] font-semibold">{orgName}</div>
        <div className="text-white/30 text-[11px] leading-snug">
          Add videoUrl in the ORGS array to show the villain video here
        </div>
      </div>
    </div>
  );
}

// =============================================================================
// MAIN APP COMPONENT
// =============================================================================

export default function App() {
  const [screen, setScreen] = useState<Screen>("welcome");
  const [navTab, setNavTab] = useState<NavTab>("home");
  const [tutPage, setTutPage] = useState(0);
  const [favs, setFavs] = useState<string[]>(["staph"]);
  const [expanded, setExpanded] = useState<Record<string, boolean>>({});
  const [darkMode, setDarkMode] = useState(false);
  const [animOn, setAnimOn] = useState(true);
  const [soundOn, setSoundOn] = useState(false);

  const [[cardIdx, swipeDir], setCardNav] = useState<[number, number]>([0, 0]);
  const [cardFlipped, setCardFlipped] = useState(false);

  const [qIdx, setQIdx] = useState(0);
  const [qPhase, setQPhase] = useState<"idle" | "correct" | "wrong">("idle");
  const [qSel, setQSel] = useState<string | null>(null);
  const [qStreak, setQStreak] = useState(0);
  const [qXP, setQXP] = useState(0);
  const [qCombo, setQCombo] = useState(0);
  const [qDone, setQDone] = useState(false);
  const [qCorrectCount, setQCorrectCount] = useState(0);
  const timerRef = useRef<ReturnType<typeof setTimeout> | null>(null);

  const totalQ = QUESTIONS.length;
  const qCard = QUESTIONS[qIdx % totalQ];
  const qOpts = useMemo(() => [...qCard.options].sort(() => Math.random() - 0.5), [qIdx]);
  const qXPGain = 10 + (qCombo > 1 ? qCombo * 5 : 0);
  const currentOrg = ORGS[cardIdx];
  const showNav = ["home", "collection", "quiz", "progress", "settings"].includes(screen);

  const nav = (s: Screen) => {
    setScreen(s);
    if (s === "home") setNavTab("home");
    else if (s === "collection") setNavTab("cards");
    else if (s === "quiz") setNavTab("quiz");
    else if (s === "progress") setNavTab("progress");
    else if (s === "settings") setNavTab("settings");
  };

  const handleNavTab = (t: NavTab) => {
    setNavTab(t);
    setScreen(t === "cards" ? "collection" : t as Screen);
  };

  const swipeTo = (idx: number) => {
    if (idx < 0 || idx >= ORGS.length) return;
    setCardNav([idx, idx > cardIdx ? 1 : -1]);
    setCardFlipped(false);
  };

  const handleAnswer = (ans: string) => {
    if (qPhase !== "idle") return;
    setQSel(ans);
    if (ans === qCard.correct) {
      const c = qCombo + 1;
      setQCombo(c);
      setQStreak(s => s + 1);
      setQXP(x => x + 10 + (c > 1 ? c * 5 : 0));
      setQCorrectCount(n => n + 1);
      setQPhase("correct");
      timerRef.current = setTimeout(() => {
        if (qIdx + 1 >= totalQ) setQDone(true);
        else { setQIdx(i => i + 1); setQPhase("idle"); setQSel(null); }
      }, 1800);
    } else {
      setQCombo(0);
      setQPhase("wrong");
    }
  };

  const handleContinue = () => {
    if (timerRef.current) clearTimeout(timerRef.current);
    if (qIdx + 1 >= totalQ) setQDone(true);
    else { setQIdx(i => i + 1); setQPhase("idle"); setQSel(null); }
  };

  const resetQuiz = () => {
    setQIdx(0); setQPhase("idle"); setQSel(null);
    setQStreak(0); setQXP(0); setQCombo(0); setQDone(false); setQCorrectCount(0);
  };

  const toggleExpand = (k: string) => setExpanded(e => ({ ...e, [k]: !e[k] }));
  const toggleFav = (id: string) => setFavs(f => f.includes(id) ? f.filter(x => x !== id) : [...f, id]);

  // ---------------------------------------------------------------------------
  // WELCOME
  // ---------------------------------------------------------------------------
  const Welcome = (
    <div className="flex flex-col h-full bg-white">
      <div className="flex-1 flex flex-col items-center justify-center px-7 pt-10">
        <div className="flex items-center gap-2.5 mb-10">
          <div className="w-9 h-9 bg-black rounded-xl flex items-center justify-center">
            <span className="text-white text-base font-bold">M</span>
          </div>
          <span className="text-xl font-bold tracking-tight text-black">MicroCards</span>
        </div>
        <div className="relative mb-8 flex items-center justify-center">
          <div className="absolute w-52 h-52 rounded-full bg-[#FFF8E1] blur-3xl opacity-80" />
          <div className="relative z-10"><StaphFace size={190} /></div>
        </div>
        <h1 className="text-[28px] font-bold text-black text-center leading-[1.25] tracking-tight mb-3">
          Learn Medicine Through<br />Memorable Villains
        </h1>
        <p className="text-[15px] text-[#555] text-center leading-relaxed max-w-[280px]">
          Every organism is a comic-style villain. Symptoms, treatments, and exam facts — locked into memory.
        </p>
      </div>
      <div className="px-7 pb-10 flex flex-col gap-3">
        {/* TODO: connect to real auth/registration */}
        <button onClick={() => { setTutPage(0); nav("tutorial"); }}
          className="w-full h-14 bg-black text-white rounded-[18px] font-semibold text-[15px] hover:opacity-90 active:scale-[0.98] transition-all">
          Get Started
        </button>
        <button onClick={() => nav("home")}
          className="w-full h-14 border-2 border-black text-black rounded-[18px] font-semibold text-[15px] hover:bg-black hover:text-white active:scale-[0.98] transition-all">
          Sign In
        </button>
      </div>
    </div>
  );

  // ---------------------------------------------------------------------------
  // TUTORIAL
  // ---------------------------------------------------------------------------
  const tutPages = [
    {
      subtitle: "Your cast of characters", title: "Meet Your Villains",
      body: "Each pathogen is a comic-style villain with a unique personality, backstory, and evil powers — all based on how it actually infects the body.",
      visual: (
        <div className="flex items-center justify-center gap-3">
          <div className="opacity-35 scale-75 origin-right"><StrepFace size={90} /></div>
          <div className="z-10"><StaphFace size={134} /></div>
          <div className="opacity-35 scale-75 origin-left"><EcoliFace size={90} /></div>
        </div>
      ),
    },
    {
      subtitle: "The visual grammar", title: "Symbolism Key",
      body: "The villain's colors and shape encode clinically important information at a glance.",
      visual: (
        <div className="flex flex-col gap-2 w-full">
          {[
            { dot: "#FFD54A", label: "Gold body", meaning: "Gram-positive cocci" },
            { dot: "#EF5350", label: "Red body", meaning: "Gram-positive cocci in chains" },
            { dot: "#43A047", label: "Green rod", meaning: "Gram-negative rod" },
            { dot: "#1565C0", label: "Blue body", meaning: "Gram-negative diplococci" },
            { dot: "#AB47BC", label: "Purple", meaning: "Fungi" },
          ].map(r => (
            <div key={r.label} className="flex items-center gap-3 bg-[#F8F8F8] border border-[#E7E7E7] rounded-xl px-3 py-2.5">
              <div className="w-5 h-5 rounded-full flex-shrink-0" style={{ backgroundColor: r.dot }} />
              <div className="flex-1 min-w-0">
                <span className="text-[12px] font-semibold text-black">{r.label}</span>
                <span className="text-[12px] text-[#666]"> means {r.meaning}</span>
              </div>
            </div>
          ))}
        </div>
      ),
    },
    {
      subtitle: "Stories, symptoms, treatment", title: "Flip and Remember",
      body: "Each card has a memory story on the front and a villain video plus clinical details on the back.",
      visual: (
        <div className="flex items-center justify-center gap-3">
          <div className="w-[165px] h-[115px] bg-[#F8F8F8] border border-[#E7E7E7] rounded-2xl flex items-center justify-center relative shadow-sm overflow-hidden">
            <EcoliFace size={86} />
            <div className="absolute bottom-2.5 right-3.5 text-[11px] text-[#999] font-medium">Flip</div>
          </div>
          <div className="w-[128px] h-[115px] bg-[#111] rounded-2xl p-3 flex flex-col justify-between shadow-sm">
            <Video className="w-4 h-4 text-white/50 mt-1 ml-1" />
            <div className="text-[10px] text-white/50 text-center pb-2">Villain video + clinical facts</div>
          </div>
        </div>
      ),
    },
    {
      subtitle: "Quiz, streaks, achievements", title: "Study. Streak. Conquer.",
      body: "Quiz mode tests your recall against the actual villain card. Build streaks, earn XP, and master your collection.",
      visual: (
        <div className="flex items-center gap-3 justify-center">
          {[
            { label: "Streak", value: "12d", icon: <Flame className="w-3.5 h-3.5 text-orange-500" /> },
            { label: "XP", value: "840", icon: <Star className="w-3.5 h-3.5 text-yellow-500 fill-yellow-500" /> },
            { label: "Accuracy", value: "94%", icon: <Target className="w-3.5 h-3.5 text-black" /> },
          ].map(s => (
            <div key={s.label} className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-2xl p-4 text-center flex-1">
              <div className="text-[20px] font-bold text-black">{s.value}</div>
              <div className="flex items-center justify-center gap-1 mt-1.5">
                {s.icon}<span className="text-[10px] text-[#666]">{s.label}</span>
              </div>
            </div>
          ))}
        </div>
      ),
    },
  ];

  const tp = tutPages[tutPage];

  const Tutorial = (
    <div className="flex flex-col h-full bg-white">
      <div className="flex justify-between items-center px-6 pt-14 pb-2">
        <button onClick={() => nav("home")} className="text-[#999] text-[13px] font-medium">Skip</button>
        <div className="flex gap-1.5">
          {tutPages.map((_, i) => (
            <div key={i} className={`h-1.5 rounded-full transition-all duration-300 ${i === tutPage ? "w-6 bg-black" : "w-1.5 bg-[#E7E7E7]"}`} />
          ))}
        </div>
        <div className="w-8" />
      </div>
      <div className="flex-1 flex flex-col items-center justify-center px-8 gap-8">
        <div className="w-full flex items-center justify-center min-h-[160px]">{tp.visual}</div>
        <div className="text-center">
          <div className="text-[11px] text-[#A8A8A8] uppercase tracking-[0.16em] font-medium mb-2">{tp.subtitle}</div>
          <h2 className="text-[26px] font-bold text-black tracking-tight mb-3">{tp.title}</h2>
          <p className="text-[15px] text-[#555] leading-relaxed">{tp.body}</p>
        </div>
      </div>
      <div className="px-7 pb-12 flex flex-col gap-2.5">
        <button onClick={() => tutPage < tutPages.length - 1 ? setTutPage(p => p + 1) : nav("home")}
          className="w-full h-14 bg-black text-white rounded-[18px] font-semibold text-[15px] hover:opacity-90 active:scale-[0.98] transition-all">
          {tutPage < tutPages.length - 1 ? "Next" : "Start Learning"}
        </button>
        {tutPage > 0 && (
          <button onClick={() => setTutPage(p => p - 1)} className="w-full h-11 text-[#666] text-[14px] font-medium">Back</button>
        )}
      </div>
    </div>
  );

  // ---------------------------------------------------------------------------
  // HOME
  // TODO: Replace static numbers with real user data from Supabase
  // ---------------------------------------------------------------------------
  const HomeScreen = (
    <div className="flex flex-col bg-white pb-28 min-h-full">
      <div className="px-6 pt-14 pb-2">
        <div className="flex items-center justify-between mb-5">
          <div>
            <p className="text-[13px] text-[#999] mb-0.5">Good morning,</p>
            {/* TODO: replace with real auth user name */}
            <h1 className="text-[24px] font-bold text-black tracking-tight">Dr. Alex Chen</h1>
          </div>
          <div className="w-11 h-11 bg-[#F8F8F8] border border-[#E7E7E7] rounded-full flex items-center justify-center">
            <User className="w-5 h-5 text-[#666]" />
          </div>
        </div>
      </div>
      <div className="px-6 flex flex-col gap-4 overflow-y-auto">
        <div className="flex gap-3">
          {[
            { icon: <Flame className="w-[18px] h-[18px] text-white" />, bg: "bg-black", val: "12", label: "Day Streak" },
            { icon: <Star className="w-[18px] h-[18px] fill-black text-black" />, bg: "bg-[#FFD54A]", val: "840", label: "XP Today" },
          ].map(s => (
            <div key={s.label} className="flex-1 bg-[#F8F8F8] border border-[#E7E7E7] rounded-2xl px-4 py-3 flex items-center gap-3">
              <div className={`w-9 h-9 ${s.bg} rounded-xl flex items-center justify-center flex-shrink-0`}>{s.icon}</div>
              <div>
                <div className="text-[18px] font-bold text-black leading-none">{s.val}</div>
                <div className="text-[11px] text-[#666] mt-0.5">{s.label}</div>
              </div>
            </div>
          ))}
        </div>
        <div className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-2xl p-4">
          <div className="flex justify-between items-start mb-3">
            <div>
              <div className="text-[13px] font-semibold text-black">{"Today's Goal"}</div>
              <div className="text-[12px] text-[#666] mt-0.5">4 of 16 cards studied</div>
            </div>
            <span className="text-[13px] font-bold text-black">25%</span>
          </div>
          <div className="h-2 bg-[#E7E7E7] rounded-full overflow-hidden">
            <motion.div className="h-full bg-black rounded-full" initial={{ width: 0 }} animate={{ width: "72%" }} transition={{ duration: 1, ease: "easeOut" }} />
          </div>
        </div>
        <button onClick={() => { setCardNav([0, 1]); setCardFlipped(false); nav("flashcard"); }}
          className="w-full bg-black text-white rounded-2xl p-4 flex items-center justify-between hover:opacity-90 active:scale-[0.98] transition-all">
          <div className="flex items-center gap-3">
            <div className="w-10 h-10 bg-white/20 rounded-xl flex items-center justify-center">
              <Play className="w-5 h-5 text-white fill-white" />
            </div>
            <div className="text-left">
              <div className="text-[14px] font-semibold">Browse Cards</div>
              <div className="text-[12px] text-white/55 mt-0.5">{ORGS.length} organisms. Swipe to explore.</div>
            </div>
          </div>
          <ArrowRight className="w-4 h-4 text-white/55" />
        </button>
        <button onClick={() => { resetQuiz(); nav("quiz"); }}
          className="w-full bg-[#F8F8F8] border border-[#E7E7E7] text-black rounded-2xl p-4 flex items-center justify-between hover:border-black active:scale-[0.98] transition-all">
          <div className="flex items-center gap-3">
            <div className="w-10 h-10 bg-black rounded-xl flex items-center justify-center">
              <FlaskConical className="w-5 h-5 text-white" />
            </div>
            <div className="text-left">
              <div className="text-[14px] font-semibold">Start Quiz</div>
              <div className="text-[12px] text-[#666] mt-0.5">{totalQ} questions. All organisms.</div>
            </div>
          </div>
          <ArrowRight className="w-4 h-4 text-[#A8A8A8]" />
        </button>
        <div>
          <div className="text-[15px] font-semibold text-black mb-3">Categories</div>
          <div className="grid grid-cols-2 gap-2.5">
            {[
              { name: "Bacteria", count: `${ORGS.filter(o => o.category === "Bacteria").length} organisms`, icon: "🦠" },
              { name: "Viruses", count: `${ORGS.filter(o => o.category === "Viruses").length} organisms`, icon: "🔬" },
              { name: "Fungi", count: `${ORGS.filter(o => o.category === "Fungi").length} organisms`, icon: "🍄" },
              { name: "Parasites", count: "Coming soon", icon: "🪱" },
            ].map(cat => (
              <button key={cat.name} onClick={() => nav("collection")}
                className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-2xl p-4 text-left hover:border-black transition-colors active:scale-[0.98]">
                <div className="text-2xl mb-2">{cat.icon}</div>
                <div className="text-[13px] font-semibold text-black">{cat.name}</div>
                <div className="text-[11px] text-[#A8A8A8] mt-0.5">{cat.count}</div>
              </button>
            ))}
          </div>
        </div>
      </div>
    </div>
  );

  // ---------------------------------------------------------------------------
  // FLASHCARD — swipeable, video on back
  // ---------------------------------------------------------------------------
  const cardSlideVariants = {
    enter: (d: number) => ({ x: d > 0 ? 340 : -340, opacity: 0.5 }),
    center: { x: 0, opacity: 1 },
    exit: (d: number) => ({ x: d > 0 ? -340 : 340, opacity: 0.5 }),
  };

  const cardInfoSections = [
    { key: "treatment", icon: "💊", title: "Treatment", body: currentOrg.treatment },
    { key: "virulence", icon: "⚔️", title: "Virulence Factors", body: currentOrg.virulence },
    { key: "pearls", icon: "💎", title: "Clinical Pearls", body: currentOrg.pearls },
    { key: "symptoms", icon: "🩺", title: "Symptoms", body: (currentOrg.symptoms as readonly string[]).join(" · ") },
  ];

  const Flashcard = (
    <div className="flex flex-col bg-white h-full">
      <div className="flex items-center justify-between px-6 pt-14 pb-3">
        <button onClick={() => nav("home")} className="w-10 h-10 bg-[#F8F8F8] border border-[#E7E7E7] rounded-xl flex items-center justify-center">
          <ChevronLeft className="w-5 h-5" />
        </button>
        <div className="text-center">
          <div className="text-[13px] font-semibold text-black">{currentOrg.category}</div>
          <div className="text-[12px] text-[#A8A8A8] mt-0.5">Card {cardIdx + 1} of {ORGS.length}</div>
        </div>
        <div role="button" tabIndex={0}
          onClick={() => toggleFav(currentOrg.id)}
          onKeyDown={e => e.key === "Enter" && toggleFav(currentOrg.id)}
          className="w-10 h-10 bg-[#F8F8F8] border border-[#E7E7E7] rounded-xl flex items-center justify-center cursor-pointer">
          <Heart className={`w-[18px] h-[18px] ${favs.includes(currentOrg.id) ? "fill-red-500 text-red-500" : "text-[#666]"}`} />
        </div>
      </div>
      <div className="px-6 mb-3">
        <div className="h-[3px] bg-[#E7E7E7] rounded-full overflow-hidden">
          <motion.div className="h-full bg-black rounded-full" animate={{ width: `${((cardIdx + 1) / ORGS.length) * 100}%` }} transition={{ duration: 0.4 }} />
        </div>
        <div className="flex justify-center gap-1.5 mt-2.5 flex-wrap">
          {ORGS.map((_, i) => (
            <button key={i} onClick={() => swipeTo(i)}
              className={`rounded-full transition-all duration-200 ${i === cardIdx ? "w-4 h-1.5 bg-black" : "w-1.5 h-1.5 bg-[#E7E7E7]"}`} />
          ))}
        </div>
      </div>
      <div className="flex-1 overflow-y-auto px-6 pb-32 flex flex-col gap-4">
        <div className="relative overflow-hidden">
          <AnimatePresence initial={false} custom={swipeDir} mode="wait">
            <motion.div key={cardIdx} custom={swipeDir}
              variants={cardSlideVariants} initial="enter" animate="center" exit="exit"
              transition={{ type: "spring", stiffness: 300, damping: 30 }}
              drag="x" dragConstraints={{ left: 0, right: 0 }} dragElastic={0.15}
              onDragEnd={(_, info) => {
                if (info.offset.x < -60 || info.velocity.x < -500) swipeTo(cardIdx + 1);
                else if (info.offset.x > 60 || info.velocity.x > 500) swipeTo(cardIdx - 1);
              }}
              style={{ cursor: "grab" }} whileDrag={{ cursor: "grabbing" }}
            >
              {!cardFlipped ? (
                <div className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-[28px] overflow-hidden select-none" style={{ boxShadow: "0 4px 24px rgba(0,0,0,0.06)" }}>
                  <div className="flex items-center justify-center py-8" style={{ background: `radial-gradient(ellipse at center, ${currentOrg.artColor}18 0%, transparent 70%)` }}>
                    {renderVillain(currentOrg.id, 164, currentOrg.name)}
                  </div>
                  <div className="px-5 pb-6">
                    <div className="text-[11px] text-[#A8A8A8] uppercase tracking-[0.14em] font-medium mb-3">Memory Story</div>
                    <p className="text-[13px] text-[#333] leading-relaxed">{currentOrg.story}</p>
                    <div className="mt-4 flex flex-wrap gap-1.5">
                      {(currentOrg.facts as readonly string[]).map(f => (
                        <span key={f} className="text-[11px] px-2.5 py-1 bg-white border border-[#E7E7E7] rounded-full text-[#444] font-medium">{f}</span>
                      ))}
                    </div>
                  </div>
                </div>
              ) : (
                <div className="flex flex-col gap-4">
                  {/* VIDEO CARD — header + video only */}
                  <div className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-[28px] overflow-hidden select-none" style={{ boxShadow: "0 4px 24px rgba(0,0,0,0.06)" }}>
                    <div className="flex items-center gap-4 p-5 border-b border-[#E7E7E7]">
                      {renderVillain(currentOrg.id, 52, currentOrg.name)}
                      <div>
                        <div className="text-[14px] font-bold text-black">{currentOrg.name}</div>
                        <div className="text-[11px] text-[#666] mt-0.5">{currentOrg.gramStain}</div>
                      </div>
                    </div>
                    {/* VIDEO AREA — add videoUrl to ORGS to make this live */}
                    <div className="px-4 py-4">
                      <VideoPlaceholder videoUrl={currentOrg.videoUrl} orgName={currentOrg.name} />
                    </div>
                  </div>
                  {/* CLINICAL INFO — separate card below video, fully accessible */}
                  <div className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-[20px] overflow-hidden" style={{ boxShadow: "0 2px 12px rgba(0,0,0,0.04)" }}>
                    <div className="px-5 py-3 border-b border-[#E7E7E7]">
                      <span className="text-[11px] text-[#A8A8A8] uppercase tracking-[0.12em] font-medium">Clinical Details</span>
                    </div>
                    <div className="flex flex-col divide-y divide-[#E7E7E7]">
                      {cardInfoSections.map(sec => (
                        <div key={sec.key}>
                          <button className="w-full flex items-center justify-between px-5 py-3.5 text-left" onClick={() => toggleExpand(sec.key)}>
                            <div className="flex items-center gap-2.5">
                              <span className="text-[16px]">{sec.icon}</span>
                              <span className="text-[13px] font-semibold text-black">{sec.title}</span>
                            </div>
                            {expanded[sec.key] ? <ChevronUp className="w-4 h-4 text-[#999]" /> : <ChevronDown className="w-4 h-4 text-[#999]" />}
                          </button>
                          <AnimatePresence>
                            {expanded[sec.key] && (
                              <motion.div initial={{ height: 0, opacity: 0 }} animate={{ height: "auto", opacity: 1 }} exit={{ height: 0, opacity: 0 }} transition={{ duration: 0.2 }} className="overflow-hidden">
                                <p className="text-[13px] text-[#444] leading-relaxed px-5 pb-4">{sec.body}</p>
                              </motion.div>
                            )}
                          </AnimatePresence>
                        </div>
                      ))}
                    </div>
                  </div>
                </div>
              )}
            </motion.div>
          </AnimatePresence>
          <div className="absolute top-1/2 -translate-y-1/2 left-1 pointer-events-none">
            {cardIdx > 0 && <div className="w-6 h-6 bg-white/80 rounded-full border border-[#E7E7E7] flex items-center justify-center shadow-sm"><ChevronLeft className="w-3.5 h-3.5 text-[#999]" /></div>}
          </div>
          <div className="absolute top-1/2 -translate-y-1/2 right-1 pointer-events-none">
            {cardIdx < ORGS.length - 1 && <div className="w-6 h-6 bg-white/80 rounded-full border border-[#E7E7E7] flex items-center justify-center shadow-sm"><ChevronLeft className="w-3.5 h-3.5 text-[#999] rotate-180" /></div>}
          </div>
        </div>
        <div>
          <div className="flex items-center justify-between mb-0.5">
            <h2 className="text-[18px] font-bold text-black tracking-tight">{currentOrg.name}</h2>
            <span className="text-[11px] font-semibold px-2.5 py-1 bg-[#F8F8F8] border border-[#E7E7E7] rounded-full text-[#666]">{currentOrg.difficulty}</span>
          </div>
          <p className="text-[12px] text-[#666]">{currentOrg.villain} · {currentOrg.gramStain}</p>
        </div>
      </div>
      <div className="absolute bottom-0 left-0 right-0 px-6 pb-8 pt-4 bg-white border-t border-[#F0F0F0] flex gap-3">
        <button onClick={() => setCardFlipped(f => !f)}
          className="flex-1 h-14 border-2 border-black text-black rounded-[18px] font-semibold text-[14px] hover:bg-black hover:text-white active:scale-[0.98] transition-all">
          {cardFlipped ? "See Front" : "Flip Card"}
        </button>
        <button onClick={() => swipeTo(cardIdx + 1)} disabled={cardIdx >= ORGS.length - 1}
          className="flex-1 h-14 bg-black text-white rounded-[18px] font-semibold text-[14px] hover:opacity-90 active:scale-[0.98] transition-all disabled:opacity-30">
          Next
        </button>
      </div>
    </div>
  );

  // ---------------------------------------------------------------------------
  // QUIZ
  // ---------------------------------------------------------------------------
  const Quiz = qDone ? (
    <div className="flex flex-col h-full bg-white items-center justify-center px-7">
      <motion.div className="w-full text-center" initial={{ opacity: 0, y: 20 }} animate={{ opacity: 1, y: 0 }}>
        <div className="text-6xl mb-5">{qCorrectCount === totalQ ? "🏆" : qCorrectCount >= totalQ * 0.8 ? "⭐" : "📚"}</div>
        <h2 className="text-[26px] font-bold text-black tracking-tight mb-1">Quiz Complete!</h2>
        <p className="text-[14px] text-[#666] mb-8">{qCorrectCount === totalQ ? "Perfect score — flawless!" : `${qCorrectCount} of ${totalQ} correct`}</p>
        <div className="grid grid-cols-3 gap-3 mb-8">
          {[{ val: `${qCorrectCount}/${totalQ}`, label: "Correct" }, { val: `${qStreak}`, label: "Best Streak" }, { val: `${qXP}`, label: "XP Earned" }].map(({ val, label }) => (
            <div key={label} className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-2xl p-4 text-center">
              <div className="text-[20px] font-bold text-black">{val}</div>
              <div className="text-[10px] text-[#666] mt-1 uppercase tracking-wide">{label}</div>
            </div>
          ))}
        </div>
        <button onClick={resetQuiz} className="w-full h-14 bg-black text-white rounded-[18px] font-semibold text-[14px] flex items-center justify-center gap-2 hover:opacity-90 active:scale-[0.98] transition-all">
          <RotateCcw className="w-4 h-4" /> Try Again
        </button>
        <button onClick={() => nav("collection")} className="w-full h-12 mt-2 text-[#666] font-medium text-[14px]">Browse Collection</button>
      </motion.div>
    </div>
  ) : (
    <div className="flex flex-col bg-white h-full">
      <div className="flex items-center justify-between px-6 pt-14 pb-3">
        <button onClick={() => nav("home")} className="w-10 h-10 bg-[#F8F8F8] border border-[#E7E7E7] rounded-xl flex items-center justify-center">
          <X className="w-[18px] h-[18px]" />
        </button>
        <div className="flex items-center gap-2">
          <AnimatePresence>
            {qStreak > 0 && (
              <motion.div className="flex items-center gap-1.5 bg-[#F8F8F8] border border-[#E7E7E7] rounded-full px-3 py-1.5" initial={{ scale: 0 }} animate={{ scale: 1 }} exit={{ scale: 0 }}>
                <Flame className="w-3.5 h-3.5 text-orange-500" /><span className="text-[12px] font-semibold">{qStreak}</span>
              </motion.div>
            )}
          </AnimatePresence>
          <AnimatePresence>
            {qCombo > 1 && (
              <motion.div className="flex items-center gap-1.5 rounded-full px-3 py-1.5 border" style={{ background: "rgba(255,213,74,0.15)", borderColor: "rgba(255,213,74,0.6)" }} initial={{ scale: 0 }} animate={{ scale: 1 }} exit={{ scale: 0 }}>
                <Zap className="w-3.5 h-3.5 text-yellow-600" /><span className="text-[12px] font-semibold">x{qCombo}</span>
              </motion.div>
            )}
          </AnimatePresence>
        </div>
        <div className="flex items-center gap-1.5 bg-[#F8F8F8] border border-[#E7E7E7] rounded-full px-3 py-1.5">
          <Star className="w-3.5 h-3.5 text-yellow-500 fill-yellow-500" />
          <span className="text-[12px] font-semibold">{qXP} XP</span>
        </div>
      </div>
      <div className="px-6 mb-1">
        <div className="flex justify-between text-[11px] text-[#A8A8A8] mb-1.5">
          <span>{qIdx + 1} of {totalQ}</span>
          <span>{Math.round((qIdx / totalQ) * 100)}%</span>
        </div>
        <div className="h-[3px] bg-[#E7E7E7] rounded-full overflow-hidden">
          <motion.div className="h-full bg-black rounded-full" animate={{ width: `${(qIdx / totalQ) * 100}%` }} transition={{ duration: 0.5 }} />
        </div>
      </div>
      <div className="flex-1 px-6 flex flex-col gap-3.5 overflow-y-auto pb-8">
        <div className="relative">
          {qPhase === "correct" && (
            <div className="absolute inset-0 rounded-[24px] overflow-hidden pointer-events-none z-30">
              {CONFETTI.map(p => (
                <motion.div key={p.id} className="absolute rounded-sm"
                  style={{ left: `${p.x}%`, top: 0, width: p.size, height: p.size, backgroundColor: p.color }}
                  initial={{ y: -10, opacity: 1, rotate: 0 }} animate={{ y: 260, opacity: 0, rotate: 200 }}
                  transition={{ duration: 1 + p.delay, delay: p.delay, ease: "easeOut" }} />
              ))}
            </div>
          )}
          <motion.div
            className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-[24px] flex flex-col items-center justify-center py-6 relative overflow-hidden"
            style={{ boxShadow: "0 4px 24px rgba(0,0,0,0.06)" }}
            animate={qPhase === "correct" ? { scale: [1, 1.04, 1], y: [0, -5, 0] } : qPhase === "wrong" ? { x: [0, -10, 10, -6, 6, 0] } : { scale: 1, y: 0, x: 0 }}
            transition={{ duration: 0.45 }}
          >
            {qPhase === "correct" && (
              <motion.div className="absolute inset-0 rounded-[24px]"
                style={{ background: "radial-gradient(ellipse 80% 60% at 50% 48%, rgba(255,213,74,0.5) 0%, transparent 74%)" }}
                initial={{ opacity: 0, scale: 0.7 }} animate={{ opacity: [0, 1, 0.7], scale: [0.7, 1.1, 1] }} transition={{ duration: 0.55 }} />
            )}
            {qPhase === "wrong" && (
              <motion.div className="absolute inset-0 rounded-[24px]"
                style={{ background: "radial-gradient(ellipse 70% 55% at 50% 50%, rgba(255,107,107,0.18) 0%, transparent 70%)" }}
                initial={{ opacity: 0, scale: 0.6 }} animate={{ opacity: [0, 1, 0], scale: [0.6, 1.3, 1] }} transition={{ duration: 0.65 }} />
            )}
            <motion.div className="relative z-10"
              animate={qPhase === "correct" ? { filter: ["drop-shadow(0 0 0px rgba(255,213,74,0))", "drop-shadow(0 0 28px rgba(255,213,74,0.9))", "drop-shadow(0 0 12px rgba(255,213,74,0.4))"] } : { filter: "none" }}
              transition={{ duration: 0.6 }}>
              {renderVillain(qCard.orgId, 140)}
            </motion.div>
            <div className="relative z-10 text-center px-5 mt-2">
              <div className="text-[14px] font-bold text-black leading-snug">{qCard.question}</div>
              <div className="text-[11px] text-[#A8A8A8] mt-1">Tap the answer you think is right.</div>
            </div>
            <AnimatePresence>
              {qPhase === "correct" && (
                <motion.div className="absolute inset-0 flex flex-col items-center justify-center rounded-[24px] z-20"
                  style={{ background: "rgba(255,255,255,0.93)" }}
                  initial={{ opacity: 0 }} animate={{ opacity: 1 }} transition={{ delay: 0.15 }}>
                  <motion.div className="text-4xl mb-2" initial={{ scale: 0 }} animate={{ scale: [0, 1.4, 1] }} transition={{ delay: 0.2, type: "spring", stiffness: 400 }}>✨</motion.div>
                  <div className="text-[22px] font-bold text-black">Perfect!</div>
                  <div className="text-[13px] font-semibold mt-1" style={{ color: "#B8860B" }}>+{qXPGain} XP{qCombo > 1 && ` Combo x${qCombo}`}</div>
                </motion.div>
              )}
            </AnimatePresence>
          </motion.div>
        </div>
        <div className="flex flex-col gap-2">
          {qOpts.map(opt => {
            const isSel = qSel === opt.id;
            const isCor = opt.id === qCard.correct;
            const answered = qPhase !== "idle";
            let bg = "bg-white", border = "border-[#E7E7E7]", txtCol = "text-black";
            let pillBg = "bg-[#F0F0F0]", pillTxt = "text-[#666]";
            if (answered) {
              if (isCor) { bg = "bg-[#A3E635]/10"; border = "border-[#A3E635]"; pillBg = "bg-[#A3E635]"; pillTxt = "text-black"; }
              else if (isSel) { bg = "bg-[#FF6B6B]/10"; border = "border-[#FF6B6B]"; pillBg = "bg-[#FF6B6B]"; pillTxt = "text-white"; }
              else { txtCol = "text-[#A8A8A8]"; border = "border-[#E7E7E7]"; bg = "bg-[#F8F8F8]"; }
            }
            return (
              <motion.button key={opt.id}
                className={`w-full rounded-[16px] border-2 ${bg} ${border} transition-all duration-200 py-3.5 px-4 flex items-center gap-3 ${!answered ? "hover:border-black" : ""}`}
                onClick={() => handleAnswer(opt.id)} disabled={answered} whileTap={!answered ? { scale: 0.98 } : {}}>
                <div className={`w-7 h-7 rounded-full flex items-center justify-center text-[12px] font-bold flex-shrink-0 ${pillBg} ${pillTxt}`}>{opt.id}</div>
                <span className={`text-[13px] font-medium ${txtCol} text-left flex-1`}>{opt.text}</span>
                {answered && isCor && <Check className="w-4 h-4 text-[#A3E635] flex-shrink-0" />}
              </motion.button>
            );
          })}
        </div>
        <AnimatePresence>
          {qPhase === "wrong" && (
            <motion.div className="w-full" initial={{ opacity: 0, y: 12 }} animate={{ opacity: 1, y: 0 }} exit={{ opacity: 0 }} transition={{ delay: 0.25 }}>
              <div className="text-center mb-3">
                <div className="text-[15px] font-semibold text-black">Almost!</div>
                <div className="text-[13px] text-[#666] mt-0.5">{"Let's lock this one into memory."}</div>
              </div>
              <button onClick={handleContinue} className="w-full h-14 bg-black text-white rounded-[18px] font-semibold text-[14px] hover:opacity-90 active:scale-[0.98] transition-all">
                Continue
              </button>
            </motion.div>
          )}
        </AnimatePresence>
      </div>
    </div>
  );

  // ---------------------------------------------------------------------------
  // COLLECTION
  // ---------------------------------------------------------------------------
  const Collection = (
    <div className="flex flex-col bg-white pb-28 min-h-full">
      <div className="px-6 pt-14 pb-3">
        <h1 className="text-[24px] font-bold text-black tracking-tight">Collection</h1>
        <p className="text-[13px] text-[#666] mt-0.5">
          {ORGS.filter(v => !("locked" in v && v.locked)).length} unlocked · {ORGS.filter(v => v.mastered).length} mastered
        </p>
      </div>
      <div className="px-6 mb-4 flex gap-2 overflow-x-auto pb-1">
        {["All", "Bacteria", "Viruses", "Fungi"].map((c, i) => (
          <button key={c} className={`flex-shrink-0 px-4 py-1.5 rounded-full text-[13px] font-medium border transition-all ${i === 0 ? "bg-black text-white border-black" : "bg-white text-[#666] border-[#E7E7E7]"}`}>{c}</button>
        ))}
      </div>
      <div className="px-6 grid grid-cols-2 gap-3 overflow-y-auto">
        {ORGS.map((v, vi) => {
          const locked = "locked" in v && v.locked;
          return (
            <div key={v.id} role="button" tabIndex={0}
              className={`bg-[#F8F8F8] border border-[#E7E7E7] rounded-2xl p-4 text-left relative overflow-hidden cursor-pointer hover:border-black transition-all active:scale-[0.97] ${locked ? "opacity-55" : ""}`}
              onClick={() => { if (!locked) { swipeTo(vi); nav("flashcard"); } }}
              onKeyDown={e => { if (e.key === "Enter" && !locked) { swipeTo(vi); nav("flashcard"); } }}>
              {v.mastered && (
                <div className="absolute top-3 left-3 bg-black text-white text-[10px] font-bold px-2 py-0.5 rounded-full z-10">Mastered</div>
              )}
              {locked && (
                <div className="absolute inset-0 flex items-center justify-center bg-white/55 rounded-2xl z-10">
                  <Lock className="w-6 h-6 text-[#A8A8A8]" />
                </div>
              )}
              {/* Favorite — using div not button to avoid invalid HTML nesting */}
              <div role="button" tabIndex={0}
                className="absolute top-3 right-3 z-20 cursor-pointer p-1"
                onClick={e => { e.stopPropagation(); toggleFav(v.id); }}
                onKeyDown={e => { if (e.key === "Enter") { e.stopPropagation(); toggleFav(v.id); } }}>
                <Heart className={`w-4 h-4 ${favs.includes(v.id) ? "fill-red-500 text-red-500" : "text-[#D0D0D0]"}`} />
              </div>
              <div className="flex items-center justify-center mb-2.5 mt-3">
                {renderVillain(v.id, 76, v.name)}
              </div>
              <div className="text-[12px] font-semibold text-black truncate">{v.short}</div>
              <div className="text-[11px] text-[#666] mt-0.5 truncate">{v.villain}</div>
              <div className="mt-3 h-1.5 bg-[#E7E7E7] rounded-full overflow-hidden">
                <div className="h-full bg-black rounded-full" style={{ width: `${v.progress}%` }} />
              </div>
              <div className="text-[10px] text-[#A8A8A8] mt-1">{v.progress}% complete</div>
            </div>
          );
        })}
      </div>
    </div>
  );

  // ---------------------------------------------------------------------------
  // PROGRESS
  // TODO: Replace with real Supabase session/stats data
  // ---------------------------------------------------------------------------
  const Progress = (
    <div className="flex flex-col bg-white pb-28 min-h-full">
      <div className="px-6 pt-14 pb-4">
        <h1 className="text-[24px] font-bold text-black tracking-tight">Progress</h1>
        <p className="text-[13px] text-[#666] mt-0.5">Week of Jul 28 - Aug 3</p>
      </div>
      <div className="px-6 flex flex-col gap-4 overflow-y-auto">
        <div className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-2xl p-5">
          <div className="flex items-center gap-5">
            <div className="relative flex-shrink-0">
              <ProgressRing pct={72} size={104} sw={9} />
              <div className="absolute inset-0 flex flex-col items-center justify-center">
                <div className="text-[20px] font-bold text-black">72%</div>
                <div className="text-[10px] text-[#666]">Overall</div>
              </div>
            </div>
            <div className="grid grid-cols-2 gap-x-4 gap-y-3 flex-1">
              {[["148", "Cards Studied"], ["12d", "Streak"], ["94%", "Accuracy"], ["3,840", "Total XP"]].map(([val, label]) => (
                <div key={label}>
                  <div className="text-[18px] font-bold text-black leading-none">{val}</div>
                  <div className="text-[11px] text-[#666] mt-0.5">{label}</div>
                </div>
              ))}
            </div>
          </div>
        </div>
        <div className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-2xl p-4">
          <div className="text-[13px] font-semibold text-black mb-4">Weekly Activity</div>
          <div style={{ height: 100 }}>
            <ResponsiveContainer width="100%" height="100%">
              <BarChart data={WEEKLY} barSize={26}>
                <XAxis dataKey="day" axisLine={false} tickLine={false} tick={{ fontSize: 11, fill: "#A8A8A8" }} />
                <Bar dataKey="v" radius={[6, 6, 0, 0]}>
                  {WEEKLY.map((entry, i) => <Cell key={i} fill={entry.v === 40 ? "#111" : "#E7E7E7"} />)}
                </Bar>
              </BarChart>
            </ResponsiveContainer>
          </div>
        </div>
        {/* TODO Caleb: connect to Supabase medals table */}
        <div>
          <div className="text-[15px] font-semibold text-black mb-3">Achievements</div>
          <div className="flex flex-col gap-2">
            {[
              { icon: "🔥", name: "10-Day Streak", desc: "Studied 10 days in a row", done: true },
              { icon: "🏆", name: "First Mastery", desc: "Mastered your first villain", done: true },
              { icon: "⚡", name: "Speed Demon", desc: "10 correct answers in a row", done: true },
              { icon: "🎯", name: "Perfect Week", desc: "100% accuracy for a full week", done: false },
              { icon: "👑", name: "Villain Hunter", desc: "Unlock every organism", done: false },
            ].map(a => (
              <div key={a.name} className={`flex items-center gap-4 bg-[#F8F8F8] border border-[#E7E7E7] rounded-2xl px-4 py-3.5 ${!a.done ? "opacity-40" : ""}`}>
                <span className="text-2xl">{a.icon}</span>
                <div className="flex-1">
                  <div className="text-[13px] font-semibold text-black">{a.name}</div>
                  <div className="text-[11px] text-[#666] mt-0.5">{a.desc}</div>
                </div>
                {a.done ? <Check className="w-4 h-4 text-black flex-shrink-0" /> : <Lock className="w-4 h-4 text-[#A8A8A8] flex-shrink-0" />}
              </div>
            ))}
          </div>
        </div>
        <div>
          <div className="text-[15px] font-semibold text-black mb-3">Recent Sessions</div>
          {[
            { deck: "Bacteriology", time: "Today, 9:15 AM", cards: 18, acc: "94%" },
            { deck: "Gram Positives", time: "Yesterday, 8:30 PM", cards: 12, acc: "88%" },
            { deck: "Virulence Factors", time: "Tue, 7:00 PM", cards: 24, acc: "96%" },
          ].map(s => (
            <div key={s.time} className="flex items-center gap-4 border-b border-[#F0F0F0] py-3 last:border-0">
              <div className="w-9 h-9 bg-[#F8F8F8] border border-[#E7E7E7] rounded-xl flex items-center justify-center flex-shrink-0">
                <BookOpen className="w-4 h-4 text-[#666]" />
              </div>
              <div className="flex-1">
                <div className="text-[13px] font-semibold text-black">{s.deck}</div>
                <div className="text-[11px] text-[#666] mt-0.5">{s.time} · {s.cards} cards</div>
              </div>
              <div className="text-[13px] font-bold text-black">{s.acc}</div>
            </div>
          ))}
        </div>
      </div>
    </div>
  );

  // ---------------------------------------------------------------------------
  // SETTINGS
  // TODO: Wire to Supabase user prefs
  // ---------------------------------------------------------------------------
  const settingSections: { title: string; items: SettingItem[] }[] = [
    {
      title: "Study",
      items: [
        { icon: <Target className="w-4 h-4" />, label: "Daily Goal", type: "nav", value: "16 cards" },
        { icon: <Bell className="w-4 h-4" />, label: "Notifications", type: "nav", value: "On" },
      ],
    },
    {
      title: "App",
      items: [
        { icon: <Moon className="w-4 h-4" />, label: "Dark Mode", type: "toggle", toggled: darkMode, onToggle: () => setDarkMode(d => !d) },
        { icon: <Zap className="w-4 h-4" />, label: "Animations", type: "toggle", toggled: animOn, onToggle: () => setAnimOn(a => !a) },
        { icon: <Volume2 className="w-4 h-4" />, label: "Sound Effects", type: "toggle", toggled: soundOn, onToggle: () => setSoundOn(s => !s) },
      ],
    },
    {
      title: "Account",
      items: [
        { icon: <Shield className="w-4 h-4" />, label: "Privacy", type: "nav" },
        { icon: <Info className="w-4 h-4" />, label: "About MicroCards", type: "nav", value: "v2.0" },
      ],
    },
  ];

  const Settings = (
    <div className="flex flex-col bg-white pb-28 min-h-full">
      <div className="px-6 pt-14 pb-5">
        <h1 className="text-[24px] font-bold text-black tracking-tight">Settings</h1>
      </div>
      <div className="px-6 flex flex-col gap-5 overflow-y-auto">
        {/* TODO: pull name/email from Supabase auth */}
        <div className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-2xl p-4 flex items-center gap-4">
          <div className="w-14 h-14 bg-black rounded-2xl flex items-center justify-center flex-shrink-0">
            <span className="text-white text-xl font-bold">A</span>
          </div>
          <div className="flex-1 min-w-0">
            <div className="text-[15px] font-bold text-black">Dr. Alex Chen</div>
            <div className="text-[12px] text-[#666] mt-0.5">alex.chen@med.edu</div>
            <div className="text-[11px] font-semibold text-black mt-1.5">Year 2 · Preclinical</div>
          </div>
          <ChevronRight className="w-4 h-4 text-[#A8A8A8] flex-shrink-0" />
        </div>
        {settingSections.map(section => (
          <div key={section.title}>
            <div className="text-[11px] text-[#A8A8A8] uppercase tracking-[0.15em] font-medium mb-2 px-1">{section.title}</div>
            <div className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-2xl overflow-hidden divide-y divide-[#E7E7E7]">
              {section.items.map(item => (
                <div key={item.label} className="flex items-center gap-3.5 px-4 py-3.5">
                  <div className="w-8 h-8 bg-white border border-[#E7E7E7] rounded-xl flex items-center justify-center flex-shrink-0 text-black">{item.icon}</div>
                  <div className="flex-1 text-[14px] font-medium text-black">{item.label}</div>
                  {item.type === "nav" && (
                    <div className="flex items-center gap-1.5">
                      {item.value && <span className="text-[13px] text-[#A8A8A8]">{item.value}</span>}
                      <ChevronRight className="w-4 h-4 text-[#A8A8A8]" />
                    </div>
                  )}
                  {item.type === "toggle" && (
                    <button onClick={item.onToggle} className={`w-11 h-6 rounded-full transition-colors duration-200 flex items-center px-0.5 ${item.toggled ? "bg-black" : "bg-[#D0D0D0]"}`}>
                      <motion.div className="w-5 h-5 rounded-full bg-white shadow-sm" animate={{ x: item.toggled ? 20 : 0 }} transition={{ type: "spring", stiffness: 500, damping: 30 }} />
                    </button>
                  )}
                </div>
              ))}
            </div>
          </div>
        ))}
        <div className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-2xl p-4 text-center">
          <div className="text-[12px] font-bold text-black mb-0.5">MicroCards</div>
          <div className="text-[11px] text-[#A8A8A8]">Christopher Reeves Sr., Caleb Reeves and Christopher Reeves</div>
          <div className="text-[11px] text-[#C8C8C8] mt-1.5">v2.0 · Mnemonic Glyphs Studio</div>
        </div>
        <button className="w-full h-14 border-2 border-[#FF6B6B] text-[#FF6B6B] rounded-[18px] font-semibold text-[14px] flex items-center justify-center gap-2 hover:bg-[#FF6B6B]/5 active:scale-[0.98] transition-all">
          <LogOut className="w-4 h-4" /> Log Out
        </button>
      </div>
    </div>
  );

  // ---------------------------------------------------------------------------
  // BOTTOM NAV
  // ---------------------------------------------------------------------------
  const BottomNav = (
    <div className="absolute bottom-0 left-0 right-0 bg-white/95 backdrop-blur-sm border-t border-[#EBEBEB] px-1 pb-7 pt-2.5 flex items-center justify-around z-50">
      {([
        { id: "home", Icon: Home, label: "Home" },
        { id: "cards", Icon: CreditCard, label: "Cards" },
        { id: "quiz", Icon: FlaskConical, label: "Quiz" },
        { id: "progress", Icon: BarChart2, label: "Progress" },
        { id: "settings", Icon: SettingsIcon, label: "Settings" },
      ] as const).map(({ id, Icon, label }) => {
        const active = navTab === id;
        return (
          <button key={id} onClick={() => handleNavTab(id as NavTab)} className="flex flex-col items-center gap-1 px-3 py-1">
            <Icon className={`w-[22px] h-[22px] transition-colors ${active ? "text-black" : "text-[#B0B0B0]"}`} />
            <span className={`text-[10px] font-medium transition-colors ${active ? "text-black" : "text-[#B0B0B0]"}`}>{label}</span>
          </button>
        );
      })}
    </div>
  );

  // ---------------------------------------------------------------------------
  // LANDING — public marketing page
  // ---------------------------------------------------------------------------
  const LandingScreen = (
    <div className="flex flex-col bg-white min-h-full overflow-y-auto">
      <div className="px-7 pt-16 pb-8 flex flex-col items-center text-center">
        <div className="flex items-center gap-2.5 mb-8">
          <div className="w-9 h-9 bg-black rounded-xl flex items-center justify-center">
            <span className="text-white text-base font-bold">M</span>
          </div>
          <span className="text-xl font-bold tracking-tight text-black">Mnemonic Glyphs</span>
        </div>
        <div className="relative mb-6 flex items-center justify-center">
          <div className="absolute w-44 h-44 rounded-full bg-[#FFF8E1] blur-3xl opacity-70" />
          <div className="relative z-10"><StaphFace size={148} /></div>
        </div>
        <div className="inline-block text-[10px] font-bold uppercase tracking-[0.18em] text-[#A8A8A8] border border-[#E7E7E7] rounded-full px-3 py-1.5 mb-4">
          MicroCards
        </div>
        <h1 className="text-[28px] font-bold text-black leading-[1.2] tracking-tight mb-4">
          Medical Microbiology<br />You Actually Remember
        </h1>
        <p className="text-[14px] text-[#555] leading-relaxed max-w-[270px]">
          Every pathogen is a comic-style villain. Learn gram stains, virulence factors, and treatment in the time it takes to read a trading card.
        </p>
      </div>
      <div className="mx-6 bg-[#F8F8F8] border border-[#E7E7E7] rounded-2xl px-4 py-4 flex justify-around mb-6">
        {[["16", "Villains"], ["16", "Total Cards"], ["13", "Quiz Questions"]].map(([n, l]) => (
          <div key={l} className="text-center">
            <div className="text-[20px] font-bold text-black">{n}</div>
            <div className="text-[10px] text-[#999] mt-0.5 uppercase tracking-wide">{l}</div>
          </div>
        ))}
      </div>
      <div className="px-6 flex flex-col gap-3 mb-8">
        <button onClick={() => { setTutPage(0); nav("tutorial"); }}
          className="w-full h-14 bg-black text-white rounded-[18px] font-semibold text-[15px] hover:opacity-90 active:scale-[0.98] transition-all">
          Join the Pilot Program
        </button>
        <button onClick={() => nav("about")}
          className="w-full h-14 border-2 border-[#E7E7E7] text-black rounded-[18px] font-semibold text-[14px] hover:border-black active:scale-[0.98] transition-all flex items-center justify-center gap-2">
          Meet the Lab →
        </button>
      </div>
      <div className="px-6 mb-6">
        <div className="text-[11px] text-[#A8A8A8] uppercase tracking-[0.15em] font-medium mb-4 text-center">How It Works</div>
        {[
          { n: "01", title: "Meet Your Villain", body: "Each pathogen has a character — art, backstory, and a villain power based on how it infects." },
          { n: "02", title: "Flip the Card", body: "Front = memory story + visual cues. Back = villain video + clinical details, treatment, pearls." },
          { n: "03", title: "Quiz & Streak", body: "Answer questions, earn XP, build streaks. Combo multipliers reward fast recall." },
        ].map(s => (
          <div key={s.n} className="flex gap-4 mb-5">
            <div className="w-8 h-8 bg-black text-white rounded-xl flex items-center justify-center text-[11px] font-bold flex-shrink-0 mt-0.5">{s.n}</div>
            <div>
              <div className="text-[13px] font-semibold text-black mb-0.5">{s.title}</div>
              <div className="text-[12px] text-[#666] leading-relaxed">{s.body}</div>
            </div>
          </div>
        ))}
      </div>
      <div className="mx-6 mb-10 bg-black rounded-[24px] p-6 text-center">
        <div className="text-[13px] font-bold text-white mb-1">Family-built in the lab.</div>
        <div className="text-[12px] text-white/55 mb-4">Christopher, Caleb & Christopher Reeves</div>
        <button onClick={() => nav("about")} className="text-[12px] font-semibold text-[#FFD54A] underline underline-offset-2">
          About the team →
        </button>
      </div>
    </div>
  );

  // ---------------------------------------------------------------------------
  // ABOUT — Meet the Lab / team page
  // ---------------------------------------------------------------------------
  const AboutScreen = (
    <div className="flex flex-col bg-white min-h-full overflow-y-auto pb-10">
      <div className="px-6 pt-14 pb-2">
        <button onClick={() => nav("landing")} className="flex items-center gap-1.5 text-[#999] text-[13px] font-medium mb-5">
          <ChevronLeft className="w-4 h-4" /> Back
        </button>
        <div className="text-[11px] text-[#A8A8A8] uppercase tracking-[0.15em] font-medium mb-1">The Family Studio</div>
        <h1 className="text-[26px] font-bold text-black tracking-tight leading-tight mb-2">Meet the Lab</h1>
        <p className="text-[13px] text-[#666] leading-relaxed">
          A family studio mastering AI tools to build products that actually help people learn. Mnemonic Glyphs is product #1.
        </p>
      </div>
      <div className="px-6 mt-6 flex flex-col gap-5">
        <div className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-[24px] overflow-hidden">
          <div className="flex items-center justify-center py-6" style={{ background: "radial-gradient(ellipse at center, #8D9DB620 0%, transparent 70%)" }}>
            <SrCat size={148} />
          </div>
          <div className="px-5 pb-6">
            <div className="flex items-center justify-between mb-1">
              <div className="text-[16px] font-bold text-black">Christopher Reeves Sr.</div>
              <span className="text-[10px] font-semibold px-2.5 py-1 bg-black text-white rounded-full">Founder</span>
            </div>
            <div className="text-[12px] text-[#A8A8A8] uppercase tracking-wide font-medium mb-2">Anesthesiologist · Businessman</div>
            <p className="text-[13px] text-[#555] leading-relaxed">
              The distinguished bearded strategist. Brings decades of clinical and business experience to the studio — and the vision that medical education should feel like collecting villain cards, not reading a textbook.
            </p>
            <div className="mt-3 flex gap-2 flex-wrap">
              <span className="text-[11px] px-2.5 py-1 bg-white border border-[#E7E7E7] rounded-full text-[#444] font-medium">⚕️ Clinical Insight</span>
              <span className="text-[11px] px-2.5 py-1 bg-white border border-[#E7E7E7] rounded-full text-[#444] font-medium">📊 Business Strategy</span>
            </div>
          </div>
        </div>
        <div className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-[24px] overflow-hidden">
          <div className="flex items-center justify-center py-6" style={{ background: "radial-gradient(ellipse at center, #E67E2220 0%, transparent 70%)" }}>
            <CalebCat size={148} />
          </div>
          <div className="px-5 pb-6">
            <div className="flex items-center justify-between mb-1">
              <div className="text-[16px] font-bold text-black">Caleb Reeves</div>
              <span className="text-[10px] font-semibold px-2.5 py-1 bg-[#FFD54A] text-black rounded-full">Builder</span>
            </div>
            <div className="text-[12px] text-[#A8A8A8] uppercase tracking-wide font-medium mb-2">Recent Graduate · Entrepreneur</div>
            <p className="text-[13px] text-[#555] leading-relaxed">
              The orange sharpshooter. Takes aim at the technical side — building the app in Lovable and generating the AI villain artwork through Grok. Business-minded, fast-moving, and locks in on the target.
            </p>
            <div className="mt-3 flex gap-2 flex-wrap">
              <span className="text-[11px] px-2.5 py-1 bg-white border border-[#E7E7E7] rounded-full text-[#444] font-medium">⚡ App Development</span>
              <span className="text-[11px] px-2.5 py-1 bg-white border border-[#E7E7E7] rounded-full text-[#444] font-medium">🎨 AI Art Direction</span>
            </div>
          </div>
        </div>
        <div className="bg-[#F8F8F8] border border-[#E7E7E7] rounded-[24px] overflow-hidden">
          <div className="flex items-center justify-center py-6" style={{ background: "radial-gradient(ellipse at center, #6C5CE720 0%, transparent 70%)" }}>
            <ChrisCat size={148} />
          </div>
          <div className="px-5 pb-6">
            <div className="flex items-center justify-between mb-1">
              <div className="text-[16px] font-bold text-black">Christopher Reeves</div>
              <span className="text-[10px] font-semibold px-2.5 py-1 bg-[#6C5CE7] text-white rounded-full">Designer</span>
            </div>
            <div className="text-[12px] text-[#A8A8A8] uppercase tracking-wide font-medium mb-2">Psychology Grad · UX/UI Designer</div>
            <p className="text-[13px] text-[#555] leading-relaxed">
              The thoughtful swordsmith. Psychology background means every design decision is grounded in how people actually learn and remember. Pivoting into UX/UI to shape how Mnemonic Glyphs feels to use.
            </p>
            <div className="mt-3 flex gap-2 flex-wrap">
              <span className="text-[11px] px-2.5 py-1 bg-white border border-[#E7E7E7] rounded-full text-[#444] font-medium">🧠 Learning Psychology</span>
              <span className="text-[11px] px-2.5 py-1 bg-white border border-[#E7E7E7] rounded-full text-[#444] font-medium">✏️ UX/UI Design</span>
            </div>
          </div>
        </div>
        <div className="bg-black rounded-[20px] p-5 text-center">
          <div className="text-[12px] text-white/50 uppercase tracking-[0.15em] font-medium mb-1">Our Mission</div>
          <p className="text-[14px] text-white font-semibold leading-relaxed">
            "Make the hardest things to memorize the easiest to remember."
          </p>
        </div>
      </div>
    </div>
  );

  // ---------------------------------------------------------------------------
  // SCREEN MAP + RENDER
  // ---------------------------------------------------------------------------
  const screens: Record<Screen, React.ReactNode> = {
    landing: LandingScreen, about: AboutScreen,
    welcome: Welcome, tutorial: Tutorial, home: HomeScreen,
    flashcard: Flashcard, quiz: Quiz, collection: Collection,
    progress: Progress, settings: Settings,
  };

  return (
    <div className="min-h-screen bg-white flex flex-col" style={{ fontFamily: "'Inter', sans-serif" }}>
      <div className="flex-1 relative overflow-hidden">
        <AnimatePresence mode="wait">
          <motion.div key={screen} className="absolute inset-0 overflow-y-auto overscroll-none bg-white"
            initial={{ opacity: 0, x: 16 }} animate={{ opacity: 1, x: 0 }} exit={{ opacity: 0, x: -16 }}
            transition={{ duration: 0.18, ease: "easeOut" }}>
            {screens[screen]}
          </motion.div>
        </AnimatePresence>
      </div>
      {showNav && BottomNav}
    </div>
  );
}
