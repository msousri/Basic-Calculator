<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Tally — Paper Tape Calculator</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;700&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
<style>
  :root{
    --ink: #14171C;
    --panel: #1E2329;
    --panel-2: #262C34;
    --paper: #EFE7D8;
    --paper-line: #DDD2B8;
    --amber: #E8A33D;
    --amber-deep: #C9821F;
    --teal: #2FA89E;
    --teal-deep: #21877E;
    --text-dim: #8A93A0;
    --key-shadow: rgba(0,0,0,0.45);
  }

  *{ box-sizing: border-box; }

  body{
    margin:0;
    min-height:100vh;
    display:flex;
    align-items:center;
    justify-content:center;
    background:
      radial-gradient(circle at 20% 10%, #1c2129 0%, var(--ink) 55%);
    font-family:'Inter', sans-serif;
    padding: 32px 16px;
  }

  .desk{
    width: 360px;
    max-width: 100%;
  }

  .machine{
    background: linear-gradient(180deg, var(--panel-2), var(--panel));
    border-radius: 18px;
    padding: 18px 18px 22px;
    box-shadow:
      0 30px 60px -20px rgba(0,0,0,0.6),
      inset 0 1px 0 rgba(255,255,255,0.04);
    border: 1px solid rgba(255,255,255,0.05);
  }

  /* Paper tape */
  .tape-slot{
    background: #0F1216;
    border-radius: 10px;
    padding: 10px 10px 0;
    box-shadow: inset 0 6px 14px rgba(0,0,0,0.6);
    position: relative;
    overflow: hidden;
  }
  .tape-slot::before{
    content:"";
    position:absolute;
    top:0; left:0; right:0;
    height: 10px;
    background: repeating-linear-gradient(90deg, #0F1216 0 6px, #05070a 6px 8px);
    z-index: 2;
  }
  .tape-scroll{
    height: 118px;
    overflow-y: auto;
    display:flex;
    flex-direction: column;
    justify-content: flex-end;
    padding: 14px 8px 8px;
    scrollbar-width: thin;
    scrollbar-color: var(--amber-deep) transparent;
  }
  .tape-scroll::-webkit-scrollbar{ width: 5px; }
  .tape-scroll::-webkit-scrollbar-thumb{ background: var(--amber-deep); border-radius: 4px; }

  .tape{
    background: var(--paper);
    background-image: repeating-linear-gradient(
      180deg, transparent 0, transparent 27px, var(--paper-line) 27px, var(--paper-line) 28px
    );
    border-radius: 3px;
    padding: 10px 12px;
    font-family: 'JetBrains Mono', monospace;
    color: #3A3226;
  }
  .tape-row{
    display:flex;
    justify-content:space-between;
    gap: 10px;
    font-size: 13px;
    line-height: 28px;
    white-space: nowrap;
    opacity: 0.55;
  }
  .tape-row:last-child{ opacity: 1; font-weight: 700; }
  .tape-row .expr{ color:#6b5f4c; }
  .tape-empty{
    font-family:'JetBrains Mono', monospace;
    font-size: 12px;
    color: #9a8f78;
    text-align:center;
    padding: 20px 0 4px;
  }

  /* Live display */
  .display{
    margin-top: 6px;
    padding: 18px 14px 10px;
    text-align: right;
  }
  .display .expr-live{
    font-family:'JetBrains Mono', monospace;
    font-size: 13px;
    color: var(--text-dim);
    min-height: 16px;
    letter-spacing: 0.02em;
  }
  .display .value{
    font-family:'JetBrains Mono', monospace;
    font-size: 42px;
    font-weight: 500;
    color: var(--paper);
    letter-spacing: 0.01em;
    overflow-x: auto;
    white-space: nowrap;
  }

  /* Keypad */
  .keys{
    margin-top: 14px;
    display:grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 10px;
  }

  button.key{
    appearance: none;
    border: none;
    border-radius: 12px;
    height: 56px;
    font-family:'Inter', sans-serif;
    font-size: 17px;
    font-weight: 600;
    color: var(--paper);
    background: #2B323C;
    box-shadow:
      0 3px 0 0 #171b20,
      0 6px 10px -2px var(--key-shadow);
    cursor: pointer;
    transition: transform 0.06s ease, box-shadow 0.06s ease;
    -webkit-tap-highlight-color: transparent;
  }
  button.key:active{
    transform: translateY(3px);
    box-shadow: 0 0 0 0 #171b20, 0 2px 4px -1px var(--key-shadow);
  }
  button.key:focus-visible{
    outline: 2px solid var(--teal);
    outline-offset: 2px;
  }

  button.key.op{
    background: linear-gradient(180deg, var(--amber), var(--amber-deep));
    color: #201400;
    box-shadow: 0 3px 0 0 #8a5f14, 0 6px 10px -2px rgba(0,0,0,0.5);
  }
  button.key.op:active{ box-shadow: 0 0 0 0 #8a5f14, 0 2px 4px -1px rgba(0,0,0,0.5); }

  button.key.fn{
    background: #3a4048;
    color: var(--paper);
    font-weight: 700;
  }

  button.key.eq{
    background: linear-gradient(180deg, var(--teal), var(--teal-deep));
    color: #06201d;
    box-shadow: 0 3px 0 0 #185650, 0 6px 10px -2px rgba(0,0,0,0.5);
  }
  button.key.eq:active{ box-shadow: 0 0 0 0 #185650, 0 2px 4px -1px rgba(0,0,0,0.5); }

  button.key.zero{ grid-column: span 2; }

  .credit{
    text-align:center;
    margin-top: 16px;
    font-family:'JetBrains Mono', monospace;
    font-size: 11.5px;
    letter-spacing: 0.04em;
    color: var(--text-dim);
  }
  .credit b{ color: var(--amber); font-weight: 600; }

  @media (prefers-reduced-motion: reduce){
    button.key{ transition: none; }
  }
</style>
</head>
<body>

<div class="desk">
  <div class="machine">

    <div class="tape-slot">
      <div class="tape-scroll" id="tapeScroll">
        <div class="tape" id="tape">
          <div class="tape-empty">— tape is empty —</div>
        </div>
      </div>
    </div>

    <div class="display">
      <div class="expr-live" id="exprLive">&nbsp;</div>
      <div class="value" id="value">0</div>
    </div>

    <div class="keys">
      <button class="key fn" data-action="clear">AC</button>
      <button class="key fn" data-action="sign">±</button>
      <button class="key fn" data-action="percent">%</button>
      <button class="key op" data-action="op" data-op="÷">÷</button>

      <button class="key" data-action="num" data-num="7">7</button>
      <button class="key" data-action="num" data-num="8">8</button>
      <button class="key" data-action="num" data-num="9">9</button>
      <button class="key op" data-action="op" data-op="×">×</button>

      <button class="key" data-action="num" data-num="4">4</button>
      <button class="key" data-action="num" data-num="5">5</button>
      <button class="key" data-action="num" data-num="6">6</button>
      <button class="key op" data-action="op" data-op="−">−</button>

      <button class="key" data-action="num" data-num="1">1</button>
      <button class="key" data-action="num" data-num="2">2</button>
      <button class="key" data-action="num" data-num="3">3</button>
      <button class="key op" data-action="op" data-op="+">+</button>

      <button class="key zero" data-action="num" data-num="0">0</button>
      <button class="key" data-action="decimal">.</button>
      <button class="key eq" data-action="equals">=</button>
    </div>
  </div>

  <div class="credit">Developed by <b>Sourish</b> :)</div>
</div>

<script>
(function(){
  const valueEl = document.getElementById('value');
  const exprLiveEl = document.getElementById('exprLive');
  const tapeEl = document.getElementById('tape');
  const tapeScroll = document.getElementById('tapeScroll');

  let current = "0";
  let previous = null;
  let operator = null;
  let justEvaluated = false;

  const opFn = {
    "+": (a,b) => a + b,
    "−": (a,b) => a - b,
    "×": (a,b) => a * b,
    "÷": (a,b) => b === 0 ? NaN : a / b,
  };

  function formatNumber(n){
    if (Number.isNaN(n)) return "Error";
    if (!Number.isFinite(n)) return "Error";
    let s = String(Math.round(n * 1e10) / 1e10);
    if (s.length > 14) s = Number(n).toExponential(6);
    return s;
  }

  function render(){
    valueEl.textContent = current;
    exprLiveEl.textContent = previous !== null && operator
      ? `${previous} ${operator}`
      : "\u00A0";
  }

  function pushTapeRow(expr, result, isFinal){
    const empty = tapeEl.querySelector('.tape-empty');
    if (empty) empty.remove();
    const row = document.createElement('div');
    row.className = 'tape-row';
    row.innerHTML = `<span class="expr">${expr}</span><span>${result}</span>`;
    if (!isFinal){
      const prevLast = tapeEl.querySelector('.tape-row:last-child');
      if (prevLast) prevLast.classList.remove('final');
    }
    tapeEl.appendChild(row);
    tapeScroll.scrollTop = tapeScroll.scrollHeight;
  }

  function inputNum(d){
    if (justEvaluated){
      current = "0";
      previous = null;
      operator = null;
      justEvaluated = false;
    }
    if (current === "0") current = d;
    else if (current.length < 14) current += d;
    render();
  }

  function inputDecimal(){
    if (justEvaluated){
      current = "0";
      previous = null;
      operator = null;
      justEvaluated = false;
    }
    if (!current.includes('.')) current += '.';
    render();
  }

  function setOperator(op){
    if (operator && previous !== null && !justEvaluated){
      evaluate();
    }
    previous = current;
    operator = op;
    justEvaluated = false;
    current = "0";
    render();
  }

  function evaluate(){
    if (operator === null || previous === null) return;
    const a = parseFloat(previous);
    const b = parseFloat(current);
    const result = opFn[operator](a, b);
    const resultStr = formatNumber(result);
    pushTapeRow(`${previous} ${operator} ${current} =`, resultStr, true);
    current = resultStr;
    previous = null;
    operator = null;
    justEvaluated = true;
    render();
  }

  function clearAll(){
    current = "0";
    previous = null;
    operator = null;
    justEvaluated = false;
    render();
  }

  function toggleSign(){
    if (current === "0") return;
    current = current.startsWith('-') ? current.slice(1) : '-' + current;
    render();
  }

  function percent(){
    const n = parseFloat(current) / 100;
    current = formatNumber(n);
    render();
  }

  document.querySelectorAll('.key').forEach(btn => {
    btn.addEventListener('click', () => {
      const action = btn.dataset.action;
      if (action === 'num') inputNum(btn.dataset.num);
      else if (action === 'decimal') inputDecimal();
      else if (action === 'op') setOperator(btn.dataset.op);
      else if (action === 'equals') evaluate();
      else if (action === 'clear') clearAll();
      else if (action === 'sign') toggleSign();
      else if (action === 'percent') percent();
    });
  });

  const keyMap = { '+':'+', '-':'−', '*':'×', '/':'÷' };
  window.addEventListener('keydown', (e) => {
    if (e.key >= '0' && e.key <= '9') inputNum(e.key);
    else if (e.key === '.') inputDecimal();
    else if (keyMap[e.key]) setOperator(keyMap[e.key]);
    else if (e.key === 'Enter' || e.key === '=') { e.preventDefault(); evaluate(); }
    else if (e.key === 'Backspace') { current = current.length > 1 ? current.slice(0,-1) : "0"; render(); }
    else if (e.key === 'Escape') clearAll();
    else if (e.key === '%') percent();
  });

  render();
})();
</script>

</body>
</html>
