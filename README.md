<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Daily Piano Quiz</title>
<style>
  :root{
    --felt:#24352B; --felt-edge:#1B2921;
    --ivory:#F8F4E9; --ivory-dim:#EFE9D8;
    --ink:#211C13; --ink-soft:#5A5340;
    --brass:#B3893A; --brass-deep:#8E6B27;
    --good:#3E7A4E; --good-bg:#E4EFE2; --key-green:#2FB457;
    --bad:#A33B2E; --bad-bg:#F4E2DC; --key-red:#D4574A;
  }
  *{box-sizing:border-box}
  body{
    margin:0; min-height:100vh;
    background:radial-gradient(1200px 700px at 50% -10%, var(--felt), var(--felt-edge));
    font-family: Verdana, Geneva, Tahoma, sans-serif; color:var(--ink);
    display:flex; justify-content:center; padding:28px 16px 48px;
  }
  .card{
    background:var(--ivory); border-radius:14px;
    box-shadow:0 10px 40px rgba(0,0,0,.35), inset 0 0 0 1px rgba(33,28,19,.08);
    width:100%; max-width:620px; padding:28px 26px; align-self:center;
  }
  .serif{font-family:Georgia,'Times New Roman',serif}
  h1{font-family:Georgia,serif; font-weight:400; text-align:center; margin:0 0 6px}
  .eyebrow{text-align:center; color:var(--brass-deep); font-size:13px;
    letter-spacing:.22em; text-transform:uppercase; margin-bottom:6px}
  .sub{text-align:center; color:var(--ink-soft); font-size:15px; margin:0 0 20px}
  .tip{color:var(--ink-soft); font-size:12.5px; text-align:center; margin-top:14px}

  .mode-row{display:flex; gap:8px; margin-bottom:14px}
  .mode-row button{
    flex:1; font-family:Georgia,serif; font-size:16px; padding:11px 0;
    border-radius:10px; cursor:pointer; background:transparent;
    border:1.5px solid rgba(33,28,19,.25); color:var(--ink-soft);
  }
  .mode-row button.active{
    background:linear-gradient(180deg,var(--brass),var(--brass-deep));
    color:var(--ivory); border-color:transparent;
  }

  .level{
    display:block; width:100%; text-align:left; background:var(--ivory-dim);
    border:1px solid rgba(33,28,19,.15); border-radius:12px;
    padding:14px 16px; margin-bottom:12px; cursor:pointer; font:inherit; color:inherit;
  }
  .level:hover{border-color:var(--brass-deep)}
  .level .row{display:flex; justify-content:space-between; align-items:baseline}
  .level .name{font-family:Georgia,serif; font-size:20px}
  .level .time{color:var(--brass-deep); font-size:13px; font-weight:600}
  .level .desc{color:var(--ink-soft); font-size:13.5px; margin-top:4px}

  .count-row{display:flex; justify-content:space-between; align-items:center;
    background:var(--ivory-dim); border:1px solid rgba(33,28,19,.15); border-radius:12px;
    padding:12px 16px; margin-bottom:12px; font-size:14.5px; color:var(--ink-soft)}
  .count-row input{width:90px; font:inherit; font-size:16px; text-align:center;
    border:1.5px solid rgba(33,28,19,.25); border-radius:8px; padding:8px 6px;
    background:#fff; color:var(--ink)}
  .count-row input:focus{outline:none; border-color:var(--brass-deep)}

  .quiz-head{display:flex; justify-content:space-between; align-items:center; margin-bottom:10px}
  .quiz-head .left{font-family:Georgia,serif; font-size:16px}
  .quiz-head .right{font-size:13.5px; color:var(--ink-soft)}
  .timer{height:7px; background:rgba(33,28,19,.12); border-radius:4px; overflow:hidden; margin-bottom:18px}
  .timer > div{height:100%; width:100%; background:var(--brass); transition:width 60ms linear}
  .prompt{font-family:Georgia,serif; text-align:center; font-size:18px; margin-bottom:6px}
  .prompt .chord{font-size:24px; font-weight:600}
  .staff-wrap svg, .piano-wrap svg{width:100%; max-width:480px; display:block; margin:0 auto}
  .staff-wrap svg{max-width:420px}
  .piano-wrap{margin:10px 0 4px}
  .pkey{cursor:pointer}
  .pkey.disabled{cursor:default}
  .feedback{min-height:30px; text-align:center; margin:6px 0 10px; font-size:16px; font-weight:600}
  .feedback .good{color:var(--good)} .feedback .bad{color:var(--bad)}
  .feedback .hint{color:var(--brass-deep); font-weight:400; font-size:14px}

  .answers{display:grid; grid-template-columns:repeat(7,1fr); gap:8px}
  .answers button{
    font-family:Georgia,serif; background:#fff; color:var(--ink);
    border:1.5px solid rgba(33,28,19,.2); border-radius:10px;
    padding:13px 0; font-size:19px; cursor:pointer;
  }
  .answers button:disabled{cursor:default}
  .answers button.correct{background:var(--good-bg); color:var(--good); border-color:var(--good)}
  .answers button.wrong{background:var(--bad-bg); color:var(--bad); border-color:var(--bad)}
  @media (max-width:420px){ .answers button{font-size:16px; padding:11px 0} }

  .spell-row{display:flex; gap:8px; justify-content:center; margin-top:4px}
  .spell-row input{
    flex:1; max-width:300px; font:inherit; font-size:18px; padding:11px 14px;
    border:1.5px solid rgba(33,28,19,.25); border-radius:10px; background:#fff; color:var(--ink);
  }
  .spell-row input:focus{outline:none; border-color:var(--brass-deep)}
  .btn-brass{
    font-family:Georgia,serif; background:linear-gradient(180deg,var(--brass),var(--brass-deep));
    color:var(--ivory); border:none; border-radius:10px; padding:12px 22px; font-size:17px;
    cursor:pointer; letter-spacing:.02em;
  }
  .btn-brass:disabled{opacity:.55; cursor:default}
  .btn-ghost{
    font-family:Georgia,serif; background:transparent; color:var(--brass-deep);
    border:1.5px solid var(--brass-deep); border-radius:10px; padding:12px 22px;
    font-size:17px; cursor:pointer;
  }
  .check-row{display:flex; justify-content:center; margin-top:10px}

  .stats{display:flex; gap:10px; margin-bottom:20px}
  .stat{flex:1; background:var(--ivory-dim); border-radius:10px; padding:12px 6px; text-align:center}
  .stat .v{font-family:Georgia,serif; font-size:22px}
  .stat .l{font-size:11.5px; color:var(--ink-soft); text-transform:uppercase; letter-spacing:.06em}
  .miss{background:var(--bad-bg); color:var(--bad); border-radius:8px; padding:6px 10px; font-size:13.5px; display:inline-block; margin:0 8px 8px 0}
  .actions{display:flex; gap:10px; justify-content:center; margin-top:26px}
  .history{margin-top:18px}
  .history h2{font-family:Georgia,serif; font-size:17px; font-weight:400; margin:0 0 8px}
  .history table{width:100%; border-collapse:collapse; font-size:13.5px}
  .history td{padding:5px 4px; border-top:1px solid rgba(33,28,19,.12); color:var(--ink-soft)}
  .history td:last-child{text-align:right; color:var(--ink)}
</style>
</head>
<body>
<div id="app" class="card"></div>

<script>
"use strict";

/* ================= note-reading data ================= */
const LETTERS = ["C","D","E","F","G","A","B"];
const dio = (l, o) => o * 7 + LETTERS.indexOf(l);

function rangeNotes(clef, l1, o1, l2, o2) {
  const out = [];
  for (let d = dio(l1, o1); d <= dio(l2, o2); d++) {
    out.push({ letter: LETTERS[d % 7], octave: Math.floor(d / 7), clef });
  }
  return out;
}

const SHARPABLE = ["C","D","F","G","A"];   // avoid B# / E#
const FLATTABLE = ["D","E","G","A","B"];   // avoid Cb / Fb

const NOTE_DIFFS = {
  easy: {
    label: "Easy", time: 10000,
    blurb: "Treble and bass clef. Notes that sit inside the staff — no ledger lines. 10 seconds each.",
    pool: [...rangeNotes("treble","E",4,"F",5), ...rangeNotes("bass","G",2,"A",3)],
    accidentals: false,
  },
  medium: {
    label: "Medium", time: 8000,
    blurb: "Treble and bass clef, up to one ledger line (middle C appears in both). 8 seconds each.",
    pool: [...rangeNotes("treble","C",4,"A",5), ...rangeNotes("bass","E",2,"C",4)],
    accidentals: false,
  },
  hard: {
    label: "Hard", time: 6000,
    blurb: "Both clefs, up to two ledger lines, sharps and flats mixed in. 6 seconds each.",
    pool: [...rangeNotes("treble","A",3,"C",6), ...rangeNotes("bass","C",2,"E",4)],
    accidentals: true,
  },
};

/* ================= chord data ================= */
// pitch-class note names; black keys prefer the common root spellings
const SHARP_NAMES = ["C","C#","D","D#","E","F","F#","G","G#","A","A#","B"];
const FLAT_NAMES  = ["C","Db","D","Eb","E","F","Gb","G","Ab","A","Bb","B"];
// roots: pc, display name, and whether their chord tones spell better with flats
const ROOTS = [
  { pc:0,  name:"C",  flat:false }, { pc:1,  name:"Db", flat:true  },
  { pc:2,  name:"D",  flat:false }, { pc:3,  name:"Eb", flat:true  },
  { pc:4,  name:"E",  flat:false }, { pc:5,  name:"F",  flat:true  },
  { pc:6,  name:"F#", flat:false }, { pc:7,  name:"G",  flat:false },
  { pc:8,  name:"Ab", flat:true  }, { pc:9,  name:"A",  flat:false },
  { pc:10, name:"Bb", flat:true  }, { pc:11, name:"B",  flat:false },
];
const WHITE_PCS = [0,2,4,5,7,9,11];

// chord tones as [semitones, letter-steps] so each tone gets its proper diatonic spelling
// (e.g. the minor third of C is Eb, not D#; the ninth of C is D)
const CHORD_TYPES = {
  maj:  { ints:[[0,0],[4,2],[7,4]],                 label: r => r + " major" },
  min:  { ints:[[0,0],[3,2],[7,4]],                 label: r => r + " minor" },
  dim:  { ints:[[0,0],[3,2],[6,4]],                 label: r => r + " diminished" },
  aug:  { ints:[[0,0],[4,2],[8,4]],                 label: r => r + " augmented" },
  dom7: { ints:[[0,0],[4,2],[7,4],[10,6]],          label: r => r + "7" },
  maj7: { ints:[[0,0],[4,2],[7,4],[11,6]],          label: r => r + "maj7" },
  min7: { ints:[[0,0],[3,2],[7,4],[10,6]],          label: r => r + "m7" },
  dim7: { ints:[[0,0],[3,2],[6,4],[9,6]],           label: r => r + "dim7" },
  m7b5: { ints:[[0,0],[3,2],[6,4],[10,6]],          label: r => r + "m7\u266D5" },
  sus2: { ints:[[0,0],[2,1],[7,4]],                 label: r => r + "sus2" },
  sus4: { ints:[[0,0],[5,3],[7,4]],                 label: r => r + "sus4" },
  six:  { ints:[[0,0],[4,2],[7,4],[9,5]],           label: r => r + "6" },
  m6:   { ints:[[0,0],[3,2],[7,4],[9,5]],           label: r => r + "m6" },
  nine: { ints:[[0,0],[4,2],[7,4],[10,6],[14,8]],   label: r => r + "9" },
};

const NATURAL_PC = { C:0, D:2, E:4, F:5, G:7, A:9, B:11 };
// spell one chord tone: letter comes from the interval degree, accidental from the pitch
function spellTone(root, semi, deg) {
  const li = LETTERS.indexOf(root.name[0]);
  const tl = LETTERS[(li + deg) % 7];
  const targetPc = (root.pc + semi) % 12;
  let acc = targetPc - NATURAL_PC[tl];
  if (acc > 6) acc -= 12;
  if (acc < -6) acc += 12;
  if (acc === 0) return tl;
  if (acc === 1) return tl + "#";
  if (acc === -1) return tl + "b";
  // would need a double accidental (e.g. Cdim7's Bbb) — fall back to the simple enharmonic
  return (root.flat ? FLAT_NAMES : SHARP_NAMES)[targetPc];
}

const CHORD_DIFFS = {
  easy: {
    label: "Easy", time: 20000,
    blurb: "Major and minor triads on natural roots (C, D, E, F, G, A, B). 20 seconds each.",
    types: ["maj","min"],
    roots: ROOTS.filter(r => WHITE_PCS.includes(r.pc)),
  },
  medium: {
    label: "Medium", time: 25000,
    blurb: "Adds 7th chords (G7, Cmaj7, Dm7...) plus diminished and augmented triads, on all 12 roots. 25 seconds each.",
    types: ["maj","min","dim","aug","dom7","maj7","min7"],
    roots: ROOTS,
  },
  hard: {
    label: "Hard", time: 30000,
    blurb: "Everything above plus dim7, m7\u266D5, sus2, sus4, 6th and 9th chords. 30 seconds each.",
    types: ["maj","min","dim","aug","dom7","maj7","min7","dim7","m7b5","sus2","sus4","six","m6","nine"],
    roots: ROOTS,
  },
};

const MODES = {
  notes:  { label: "Note reading",   diffs: NOTE_DIFFS },
  chords: { label: "Chord practice", diffs: CHORD_DIFFS },
};

const DEFAULT_TOTAL = 100;
const MIN_TOTAL = 5, MAX_TOTAL = 500;
const KEYBOARD_LOW = 60, KEYBOARD_HIGH = 83; // C4..B5 (two octaves)

const accSym = a => a === "sharp" ? "\u266F" : a === "flat" ? "\u266D" : "";
const noteFullName = q =>
  q.letter + accSym(q.accidental) + q.octave +
  (q.letter === "C" && q.octave === 4 && !q.accidental ? " (middle C)" : "");

/* ================= question building ================= */
function buildNoteQuestion(diff, prevKey) {
  let n, k, guard = 0;
  do {
    n = diff.pool[Math.floor(Math.random() * diff.pool.length)];
    k = "n" + n.letter + n.octave + n.clef;
    guard++;
  } while (k === prevKey && guard < 20);
  let accidental = null;
  if (diff.accidentals && Math.random() < 0.45) {
    const cs = SHARPABLE.includes(n.letter), cf = FLATTABLE.includes(n.letter);
    if (cs && cf) accidental = Math.random() < 0.5 ? "sharp" : "flat";
    else if (cs) accidental = "sharp";
    else if (cf) accidental = "flat";
  }
  return { q: { kind:"note", letter:n.letter, octave:n.octave, clef:n.clef, accidental }, key:k };
}

function buildChordQuestion(diff, prevKey) {
  let root, typeKey, kind, key, guard = 0;
  do {
    typeKey = diff.types[Math.floor(Math.random() * diff.types.length)];
    kind = Math.random() < 0.5 ? "keys" : "spell";
    const maxInt = Math.max(...CHORD_TYPES[typeKey].ints.map(p => p[0]));
    // for keyboard questions the whole chord must fit on the two rendered octaves
    const okRoots = kind === "keys"
      ? diff.roots.filter(r => r.pc + maxInt <= KEYBOARD_HIGH - KEYBOARD_LOW)
      : diff.roots;
    root = okRoots[Math.floor(Math.random() * okRoots.length)];
    key = "c" + root.name + typeKey;
    guard++;
  } while (key === prevKey && guard < 20);

  const t = CHORD_TYPES[typeKey];
  const pcs = [...new Set(t.ints.map(p => (root.pc + p[0]) % 12))];
  const midis = t.ints.map(p => KEYBOARD_LOW + root.pc + p[0]);
  const spelled = t.ints.map(p => spellTone(root, p[0], p[1]));
  return {
    q: { kind, display: t.label(root.name), pcs, midis, spelled },
    key,
  };
}

function buildQuestions(mode, diffKey, total) {
  const diff = MODES[mode].diffs[diffKey];
  const qs = [];
  let prev = null;
  for (let i = 0; i < total; i++) {
    const built = mode === "notes"
      ? buildNoteQuestion(diff, prev)
      : buildChordQuestion(diff, prev);
    prev = built.key;
    qs.push(built.q);
  }
  return qs;
}

/* ================= staff drawing =================
   Top staff line y = 0, bottom line y = 48, line spacing 12,
   one diatonic step = 6px.
   Treble bottom line = E4 (dio 30) · Bass bottom line = G2 (dio 18) */
function staffSVG(q) {
  const noteX = 212;
  const base = q.clef === "treble" ? 30 : 18;
  const y = 48 - (dio(q.letter, q.octave) - base) * 6;

  const ledgers = [];
  for (let ly = 60; ly <= y; ly += 12) ledgers.push(ly);
  for (let ly = -12; ly >= y; ly -= 12) ledgers.push(ly);

  const stemUp = y > 24;
  const stemX = stemUp ? noteX + 7 : noteX - 7;
  const stemY2 = stemUp ? y - 42 : y + 42;

  const lines = [0,12,24,36,48].map(ly =>
    `<line x1="18" y1="${ly}" x2="322" y2="${ly}" stroke="var(--ink)" stroke-width="1.4"/>`).join("");
  const ledgerEls = ledgers.map(ly =>
    `<line x1="${noteX-13}" y1="${ly}" x2="${noteX+13}" y2="${ly}" stroke="var(--ink)" stroke-width="1.4"/>`).join("");
  const clef = q.clef === "treble"
    ? `<text x="46" y="26" font-size="66" text-anchor="middle" dominant-baseline="central" fill="var(--ink)" font-family="serif">\u{1D11E}</text>`
    : `<text x="46" y="17" font-size="46" text-anchor="middle" dominant-baseline="central" fill="var(--ink)" font-family="serif">\u{1D122}</text>`;
  const acc = q.accidental
    ? `<text x="${noteX-21}" y="${y}" font-size="24" text-anchor="middle" dominant-baseline="central" fill="var(--ink)" font-family="serif">${accSym(q.accidental)}</text>`
    : "";

  return `<svg viewBox="0 -56 340 160" role="img" aria-label="A note on the ${q.clef} staff">
    ${lines}
    <line x1="18" y1="0" x2="18" y2="48" stroke="var(--ink)" stroke-width="2.5"/>
    <line x1="322" y1="0" x2="322" y2="48" stroke="var(--ink)" stroke-width="2.5"/>
    ${clef}
    <text x="20" y="86" font-size="11" fill="var(--ink-soft)" font-style="italic" font-family="Georgia,serif">${q.clef} clef</text>
    ${ledgerEls}${acc}
    <line x1="${stemX}" y1="${y}" x2="${stemX}" y2="${stemY2}" stroke="var(--ink)" stroke-width="1.8"/>
    <ellipse cx="${noteX}" cy="${y}" rx="7.4" ry="5.4" fill="var(--ink)" transform="rotate(-16 ${noteX} ${y})"/>
  </svg>`;
}

/* ================= piano drawing =================
   Two octaves C4..B5. feedback = null while answering, or
   { correct:Set<midi>, wrong:Set<midi> } during feedback. */
const WHITE_W = 34, WHITE_H = 122, BLACK_W = 20, BLACK_H = 76;

function pianoSVG(selected, feedback) {
  const whites = [], blacks = [];
  let wi = 0;
  for (let m = KEYBOARD_LOW; m <= KEYBOARD_HIGH; m++) {
    const pc = m % 12;
    if (WHITE_PCS.includes(pc)) { whites.push({ m, x: wi * WHITE_W }); wi++; }
    else blacks.push({ m, x: wi * WHITE_W - BLACK_W / 2 });
  }
  const totalW = wi * WHITE_W;

  const fillFor = (m, isBlack) => {
    if (feedback) {
      if (feedback.wrong.has(m)) return "var(--key-red)";
      if (feedback.correct.has(m)) return "var(--key-green)";
    } else if (selected.has(m)) return "var(--key-green)";
    return isBlack ? "var(--ink)" : "#FFFFFF";
  };

  const whiteEls = whites.map(k =>
    `<rect class="pkey${feedback ? " disabled" : ""}" data-midi="${k.m}" x="${k.x}" y="0"
      width="${WHITE_W}" height="${WHITE_H}" fill="${fillFor(k.m, false)}"
      stroke="var(--ink)" stroke-width="1.2" rx="2"/>`).join("");
  const blackEls = blacks.map(k =>
    `<rect class="pkey${feedback ? " disabled" : ""}" data-midi="${k.m}" x="${k.x}" y="0"
      width="${BLACK_W}" height="${BLACK_H}" fill="${fillFor(k.m, true)}"
      stroke="var(--ink)" stroke-width="1.2" rx="2"/>`).join("");

  return `<svg viewBox="-1 -1 ${totalW + 2} ${WHITE_H + 16}" role="img" aria-label="Two-octave piano keyboard">
    ${whiteEls}${blackEls}
    <text x="${WHITE_W/2}" y="${WHITE_H + 12}" font-size="10" text-anchor="middle" fill="var(--ink-soft)" font-family="Georgia,serif">C4</text>
    <text x="${7.5 * WHITE_W}" y="${WHITE_H + 12}" font-size="10" text-anchor="middle" fill="var(--ink-soft)" font-family="Georgia,serif">C5</text>
  </svg>`;
}

/* ================= typed-answer parsing ================= */
// returns { pcs:Set } or { error:token }
function parseNotes(text) {
  const tokens = text.trim().split(/[\s,;/]+/).filter(Boolean);
  if (!tokens.length) return { error: "" };
  const pcs = new Set();
  const base = { C:0, D:2, E:4, F:5, G:7, A:9, B:11 };
  for (const tok of tokens) {
    const m = tok.match(/^([A-Ga-g])(#|\u266F|b|B|\u266D)?$/);
    if (!m) return { error: tok };
    let pc = base[m[1].toUpperCase()];
    if (m[2]) pc += (m[2] === "#" || m[2] === "\u266F") ? 1 : -1;
    pcs.add(((pc % 12) + 12) % 12);
  }
  return { pcs };
}
const sameSet = (a, b) => a.size === b.size && [...a].every(x => b.has(x));

/* ================= optional score history (safe if storage is blocked) ================= */
const HISTORY_KEY = "sightReadingHistory";
function loadHistory() {
  try { return JSON.parse(localStorage.getItem(HISTORY_KEY)) || []; }
  catch (e) { return []; }
}
function saveSession(entry) {
  try {
    const h = loadHistory();
    h.unshift(entry);
    localStorage.setItem(HISTORY_KEY, JSON.stringify(h.slice(0, 14)));
  } catch (e) { /* storage unavailable — history simply won't persist */ }
}

/* ================= state ================= */
const S = {
  screen: "menu", mode: "notes", diffKey: "easy",
  questions: [], qIndex: 0,
  phase: "answering", results: [],
  lastWrongPick: null, selection: new Set(),
  streak: 0, bestStreak: 0, total: DEFAULT_TOTAL,
  timerId: null, advanceId: null, startTime: 0, deadline: 0,
};
const app = document.getElementById("app");
const activeDiff = () => MODES[S.mode].diffs[S.diffKey];

function clearTimers() {
  if (S.timerId) { clearInterval(S.timerId); S.timerId = null; }
  if (S.advanceId) { clearTimeout(S.advanceId); S.advanceId = null; }
}

function startSession(key) {
  clearTimers();
  const input = document.getElementById("qcount");
  if (input) {
    const v = parseInt(input.value, 10);
    S.total = isNaN(v) ? DEFAULT_TOTAL : Math.min(MAX_TOTAL, Math.max(MIN_TOTAL, v));
  }
  S.diffKey = key;
  S.questions = buildQuestions(S.mode, key, S.total);
  S.qIndex = 0; S.results = [];
  S.streak = 0; S.bestStreak = 0;
  S.selection = new Set(); S.lastWrongPick = null;
  S.phase = "answering";
  S.screen = "quiz";
  render();
  startTimer();
}

function startTimer() {
  const d = activeDiff();
  S.startTime = Date.now();
  S.deadline = S.startTime + d.time;
  S.timerId = setInterval(() => {
    const rem = S.deadline - Date.now();
    const bar = document.getElementById("timerbar");
    if (rem <= 0) {
      clearInterval(S.timerId); S.timerId = null;
      if (bar) bar.style.width = "0%";
      finishQuestion(false, true, null);
    } else if (bar) {
      const frac = rem / d.time;
      bar.style.width = (frac * 100) + "%";
      bar.style.background = frac > 0.35 ? "var(--brass)" : "var(--bad)";
    }
  }, 50);
}

/* record an outcome and advance */
function finishQuestion(correct, timedOut, wrongPick) {
  if (S.screen !== "quiz" || S.phase !== "answering") return;
  clearTimers();
  S.phase = "feedback";
  const q = S.questions[S.qIndex];
  S.lastWrongPick = wrongPick;
  S.results.push({ q, correct, timedOut, timeMs: Date.now() - S.startTime });
  S.streak = correct ? S.streak + 1 : 0;
  S.bestStreak = Math.max(S.bestStreak, S.streak);
  render();
  const pause = q.kind === "note" ? 1150 : 2300;
  S.advanceId = setTimeout(() => {
    if (S.qIndex + 1 >= S.total) {
      const score = S.results.filter(r => r.correct).length;
      saveSession({
        date: new Date().toISOString().slice(0, 10),
        mode: MODES[S.mode].label,
        diff: activeDiff().label,
        score, total: S.total, pct: Math.round(score / S.total * 100),
      });
      S.screen = "results";
    } else {
      S.qIndex++;
      S.selection = new Set();
      S.lastWrongPick = null;
      S.phase = "answering";
    }
    render();
    if (S.screen === "quiz") startTimer();
  }, pause);
}

/* --- answer handlers --- */
function answerNote(letter) {
  const q = S.questions[S.qIndex];
  if (q.kind !== "note") return;
  finishQuestion(letter === q.letter, false, letter);
}

function answerKeys() {
  const q = S.questions[S.qIndex];
  const pickedPcs = new Set([...S.selection].map(m => m % 12));
  const correct = sameSet(pickedPcs, new Set(q.pcs));
  finishQuestion(correct, false, null);
}

function answerSpell() {
  const inp = document.getElementById("spellinput");
  if (!inp) return;
  const parsed = parseNotes(inp.value);
  if (parsed.error !== undefined) {
    const fb = document.getElementById("feedbackarea");
    if (fb) fb.innerHTML = parsed.error
      ? `<span class="hint">Couldn't read "${esc(parsed.error)}" — use names like F#, Bb, or plain letters</span>`
      : `<span class="hint">Type the notes, e.g. "A C E" — order and case don't matter</span>`;
    return;
  }
  const q = S.questions[S.qIndex];
  finishQuestion(sameSet(parsed.pcs, new Set(q.pcs)), false, inp.value.trim());
}

/* ================= rendering ================= */
function esc(s) { return String(s).replace(/[&<>"]/g, c => ({ "&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;" }[c])); }
const missLabel = r => r.q.kind === "note"
  ? `${noteFullName(r.q)} \u00B7 ${r.q.clef}`
  : `${r.q.display} \u2192 ${r.q.spelled.join(" ")}`;

function render() {
  if (S.screen === "menu") return renderMenu();
  if (S.screen === "quiz") return renderQuiz();
  renderResults();
}

function renderMenu() {
  const diffs = MODES[S.mode].diffs;
  const levels = Object.entries(diffs).map(([key, d]) => `
    <button class="level" data-level="${key}">
      <div class="row"><span class="name">${d.label}</span>
        <span class="time">${d.time/1000}s per question</span></div>
      <div class="desc">${d.blurb}</div>
    </button>`).join("");

  const h = loadHistory();
  const historyHtml = h.length ? `
    <div class="history">
      <h2>Recent sessions</h2>
      <table>${h.map(e => `<tr>
        <td>${esc(e.date)}</td><td>${esc(e.mode || "Notes")} \u00B7 ${esc(e.diff)}</td>
        <td>${e.score}/${e.total || "?"} (${e.pct}%)</td></tr>`).join("")}
      </table>
    </div>` : "";

  const modeSub = S.mode === "notes"
    ? "Name each note on the staff before the clock runs out.<br>Tap the buttons or press <strong>A&ndash;G</strong> on your keyboard."
    : "You'll be given a chord — either click its keys on the piano,<br>or type the notes that make it up.";

  app.innerHTML = `
    <div class="eyebrow">Notes &middot; Chords &middot; Every day</div>
    <h1 style="font-size:34px">Daily Piano Quiz</h1>
    <div class="mode-row">
      <button data-mode="notes" class="${S.mode === "notes" ? "active" : ""}">Note reading</button>
      <button data-mode="chords" class="${S.mode === "chords" ? "active" : ""}">Chord practice</button>
    </div>
    <p class="sub">${modeSub}</p>
    <div class="count-row">
      <label for="qcount">Questions per session</label>
      <input id="qcount" type="number" min="${MIN_TOTAL}" max="${MAX_TOTAL}" step="5" value="${S.total}">
    </div>
    ${levels}
    <p class="tip">Come back once a day and try to beat yesterday's score and streak.</p>
    ${historyHtml}`;

  app.querySelectorAll(".mode-row button").forEach(b =>
    b.addEventListener("click", () => {
      const input = document.getElementById("qcount");
      if (input) {
        const v = parseInt(input.value, 10);
        if (!isNaN(v)) S.total = Math.min(MAX_TOTAL, Math.max(MIN_TOTAL, v));
      }
      S.mode = b.dataset.mode;
      render();
    }));
  app.querySelectorAll(".level").forEach(b =>
    b.addEventListener("click", () => startSession(b.dataset.level)));
}

function quizShell(inner, feedbackHtml) {
  const d = activeDiff();
  const score = S.results.filter(r => r.correct).length;
  return `
    <div class="quiz-head">
      <span class="left">${MODES[S.mode].label} \u00B7 ${d.label} \u00B7 <strong>${S.qIndex+1}</strong> / ${S.total}</span>
      <span class="right">Score ${score} \u00B7 Streak ${S.streak}</span>
    </div>
    <div class="timer"><div id="timerbar"></div></div>
    ${inner}
    <div class="feedback" id="feedbackarea">${feedbackHtml}</div>`;
}

function feedbackText() {
  const last = S.results[S.results.length - 1];
  if (!last) return "";
  const q = last.q;
  const answer = q.kind === "note" ? noteFullName(q) : q.spelled.join(" ");
  if (last.correct) return `<span class="good">Correct \u2014 ${answer}</span>`;
  if (last.timedOut) return `<span class="bad">Time's up \u2014 ${q.kind === "note" ? "it was " + answer : q.display + " is " + answer}</span>`;
  return `<span class="bad">Not quite \u2014 ${q.kind === "note" ? "it was " + answer : q.display + " is " + answer}</span>`;
}

function renderQuiz() {
  const q = S.questions[S.qIndex];
  const fb = S.phase === "feedback";

  if (q.kind === "note") {
    const last = S.results[S.results.length - 1];
    const buttons = LETTERS.map(L => {
      let cls = "";
      if (fb && last) {
        if (L === last.q.letter) cls = "correct";
        else if (L === S.lastWrongPick) cls = "wrong";
      }
      return `<button class="${cls}" data-letter="${L}" ${fb ? "disabled" : ""}>
        ${L}${q.accidental ? accSym(q.accidental) : ""}</button>`;
    }).join("");

    app.innerHTML = quizShell(`
      <div class="prompt">What note is this?</div>
      <div class="staff-wrap">${staffSVG(q)}</div>`, fb ? feedbackText() : "") + `
      <div class="answers">${buttons}</div>
      <p class="tip">Keyboard shortcuts: A B C D E F G</p>`;

    app.querySelectorAll(".answers button").forEach(b =>
      b.addEventListener("click", () => answerNote(b.dataset.letter)));
    return;
  }

  if (q.kind === "keys") {
    let feedback = null;
    if (fb) {
      const correct = new Set(q.midis);
      // any selected key whose pitch class isn't in the chord is a wrong pick;
      // selected keys with a chord pitch class stay green (any octave counts)
      const wrong = new Set([...S.selection].filter(m => !q.pcs.includes(m % 12)));
      const okSel = new Set([...S.selection].filter(m => q.pcs.includes(m % 12)));
      feedback = { correct: new Set([...correct, ...okSel]), wrong };
    }
    app.innerHTML = quizShell(`
      <div class="prompt">Click the keys that play<br><span class="chord">${q.display}</span></div>
      <div class="piano-wrap">${pianoSVG(S.selection, feedback)}</div>`, fb ? feedbackText() : "") + `
      <div class="check-row">
        <button class="btn-brass" id="checkkeys" ${fb ? "disabled" : ""}>Check answer</button>
      </div>
      <p class="tip">Tap keys to select or unselect them. Any octave counts.</p>`;

    if (!fb) {
      app.querySelectorAll(".pkey").forEach(k =>
        k.addEventListener("click", () => {
          const m = parseInt(k.dataset.midi, 10);
          if (S.selection.has(m)) {
            S.selection.delete(m);
            k.setAttribute("fill", WHITE_PCS.includes(m % 12) ? "#FFFFFF" : "var(--ink)");
          } else {
            S.selection.add(m);
            k.setAttribute("fill", "var(--key-green)");
          }
        }));
      const btn = document.getElementById("checkkeys");
      if (btn) btn.addEventListener("click", answerKeys);
    }
    return;
  }

  // spell
  app.innerHTML = quizShell(`
    <div class="prompt">Which notes make up<br><span class="chord">${q.display}</span> ?</div>
    <div class="spell-row">
      <input id="spellinput" type="text" autocomplete="off" autocapitalize="off"
        spellcheck="false" placeholder="e.g. A C E" ${fb ? "disabled" : ""}>
      <button class="btn-brass" id="checkspell" ${fb ? "disabled" : ""}>Check</button>
    </div>`, fb ? feedbackText() : "") + `
    <p class="tip">Separate notes with spaces or commas &middot; any case works &middot; sharps as "#", flats as "b" (e.g. f#, bb) &middot; order doesn't matter</p>`;

  if (!fb) {
    const inp = document.getElementById("spellinput");
    const btn = document.getElementById("checkspell");
    if (btn) btn.addEventListener("click", answerSpell);
    if (inp) {
      inp.addEventListener("keydown", e => { if (e.key === "Enter") answerSpell(); });
      inp.focus();
    }
  }
}

function renderResults() {
  const d = activeDiff();
  const score = S.results.filter(r => r.correct).length;
  const pct = Math.round(score / S.total * 100);
  const answered = S.results.filter(r => !r.timedOut);
  const avg = answered.length
    ? (answered.reduce((a, r) => a + r.timeMs, 0) / answered.length / 1000).toFixed(1) + "s"
    : "&mdash;";
  const misses = S.results.filter(r => !r.correct);
  const verdict =
    pct >= 90 ? "Excellent &mdash; this material is nearly automatic."
    : pct >= 70 ? "Solid work. A few of these still need drilling."
    : pct >= 50 ? "Good start &mdash; repeat this level daily until it feels easy."
    : "Keep at it &mdash; accuracy comes before speed.";

  const missHtml = misses.length ? `
    <div style="margin-top:18px">
      <div class="serif" style="font-size:17px;margin-bottom:8px">To review</div>
      <div>${misses.map(m =>
        `<span class="miss">${missLabel(m)}${m.timedOut ? " \u00B7 time" : ""}</span>`).join("")}
      </div></div>` : "";

  app.innerHTML = `
    <h1 style="font-size:30px">Session complete</h1>
    <div class="sub" style="margin-bottom:20px">${MODES[S.mode].label} \u00B7 ${d.label} \u00B7 ${d.time/1000}s per question</div>
    <div class="stats">
      <div class="stat"><div class="v">${score}/${S.total}</div><div class="l">correct</div></div>
      <div class="stat"><div class="v">${pct}%</div><div class="l">accuracy</div></div>
      <div class="stat"><div class="v">${S.bestStreak}</div><div class="l">best streak</div></div>
      <div class="stat"><div class="v">${avg}</div><div class="l">avg answer</div></div>
    </div>
    <p class="sub" style="margin:0">${verdict}</p>
    ${missHtml}
    <div class="actions">
      <button class="btn-brass" id="again">Practice again</button>
      <button class="btn-ghost" id="menu">Change level</button>
    </div>`;

  document.getElementById("again").addEventListener("click", () => startSession(S.diffKey));
  document.getElementById("menu").addEventListener("click", () => { S.screen = "menu"; render(); });
}

/* keyboard answers — note mode only, and never while typing in a field */
window.addEventListener("keydown", e => {
  if (S.screen !== "quiz" || S.mode !== "notes" || S.phase !== "answering") return;
  if (e.target && (e.target.tagName === "INPUT" || e.target.tagName === "TEXTAREA")) return;
  const k = e.key.toUpperCase();
  if (LETTERS.includes(k)) answerNote(k);
});

render();
</script>
</body>
</html>
