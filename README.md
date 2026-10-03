# buycheckapp.github.io
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>BuyCheck: Business Acquisition Calculator (SDE, Multiple &amp; DSCR)</title>
<meta name="description" content="Free business acquisition calculator. Enter a listing's price and cash flow to see the SDE multiple, loan payment, debt service coverage ratio (DSCR), and a buy or pass verdict.">
<style>
:root{--bg:#eef2f6;--card:#fff;--ink:#12263a;--mute:#5d6f82;--line:#d5dde6;--acc:#2447d6;--good:#1f8a5b;--warn:#b97d00;--bad:#c23b3b;box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--bg:#0e1824;--card:#16253a;--ink:#e8eef5;--mute:#93a5b8;--line:#2a3d55;--acc:#7b97ff;--good:#4cc790;--warn:#e3b04b;--bad:#ef7676}}
:root[data-theme="dark"]{--bg:#0e1824;--card:#16253a;--ink:#e8eef5;--mute:#93a5b8;--line:#2a3d55;--acc:#7b97ff;--good:#4cc790;--warn:#e3b04b;--bad:#ef7676}
*{box-sizing:border-box}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
body{margin:0;background:var(--bg);color:var(--ink);font:16px/1.45 -apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,sans-serif}
main{max-width:560px;margin:0 auto;padding:20px 16px 48px}
h1{font:700 26px/1.15 Georgia,"Times New Roman",serif;margin:8px 0 6px}
.sub{color:var(--mute);margin:0 0 18px}
.card{background:var(--card);border:1px solid var(--line);border-radius:12px;padding:16px;margin-bottom:14px}
h2{font-size:15px;margin:0 0 12px}
.grid{display:grid;grid-template-columns:1fr 1fr;gap:12px}
label{display:block;font-size:13px;color:var(--mute);margin-bottom:4px}
input{width:100%;font:inherit;font-size:16px;color:var(--ink);background:transparent;border:1px solid var(--line);border-radius:8px;padding:10px}
input:focus,button:focus-visible{outline:2px solid var(--acc);outline-offset:1px}
.full{grid-column:1/-1}
.verdict{border-left:6px solid var(--mute)}
.verdict.good{border-color:var(--good)}.verdict.warn{border-color:var(--warn)}.verdict.bad{border-color:var(--bad)}
.vt{font:700 28px/1.1 Georgia,serif;margin:0 0 4px}
.good .vt{color:var(--good)}.warn .vt{color:var(--warn)}.bad .vt{color:var(--bad)}
.vd{margin:0;color:var(--mute);font-size:14px}
.meter{position:relative;height:10px;border-radius:5px;margin:18px 0 6px;background:linear-gradient(90deg,var(--bad) 0 33%,var(--warn) 33% 55%,var(--good) 55%)}
.pin{position:absolute;top:-5px;width:4px;height:20px;border-radius:2px;background:var(--ink);transform:translateX(-2px)}
.ml{display:flex;justify-content:space-between;font-size:12px;color:var(--mute)}
dl{margin:0;display:grid;grid-template-columns:1fr auto;gap:8px 12px}
dt{color:var(--mute)}dd{margin:0;font-weight:600;text-align:right;font-variant-numeric:tabular-nums}
button{font:inherit;font-weight:600;border:0;border-radius:8px;padding:11px 16px;background:var(--acc);color:#fff;cursor:pointer}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]) button{color:#0e1824}}
:root[data-theme="dark"] button{color:#0e1824}
.row{display:flex;gap:8px}.row input{flex:1}
.deal{display:flex;justify-content:space-between;align-items:center;gap:8px;padding:10px 0;border-top:1px solid var(--line)}
.deal b{display:block}.deal small{color:var(--mute)}
.x{background:transparent;color:var(--mute);padding:6px 10px;border:1px solid var(--line)}
p{margin:0 0 12px;font-size:14px;color:var(--mute)}h2+p{margin-top:0}.note{font-size:12px;color:var(--mute)}
</style>
</head>
<body>
<main>
<h1>BuyCheck: Business Acquisition Calculator</h1>
<p class="sub">Is this small business worth buying? Enter the numbers from any listing to get the SDE multiple, loan payment, debt coverage ratio, and a verdict.</p>

<section class="card">
<h2>The listing</h2>
<div class="grid">
<div><label for="price">Asking price ($)</label><input id="price" type="number" inputmode="decimal" value="600000"></div>
<div><label for="rev">Annual revenue ($)</label><input id="rev" type="number" inputmode="decimal" value="1200000"></div>
<div><label for="sde">Annual cash flow / SDE ($)</label><input id="sde" type="number" inputmode="decimal" value="200000"></div>
<div><label for="sal">Your salary needed ($)</label><input id="sal" type="number" inputmode="decimal" value="60000"></div>
</div>
</section>

<section class="card">
<h2>The loan</h2>
<div class="grid">
<div><label for="down">Down payment (%)</label><input id="down" type="number" inputmode="decimal" value="10"></div>
<div><label for="rate">Interest rate (%)</label><input id="rate" type="number" inputmode="decimal" value="10.5"></div>
<div class="full"><label for="term">Loan term (years)</label><input id="term" type="number" inputmode="decimal" value="10"></div>
</div>
</section>

<section class="card verdict" id="vcard" aria-live="polite">
<p class="vt" id="vt"></p>
<p class="vd" id="vd"></p>
<div class="meter"><div class="pin" id="pin"></div></div>
<div class="ml"><span>Debt coverage 0.8</span><span>1.25</span><span>2.0+</span></div>
</section>

<section class="card">
<h2>The numbers</h2>
<dl>
<dt>Price multiple (price ÷ cash flow)</dt><dd id="mult"></dd>
<dt>Price ÷ revenue</dt><dd id="rmult"></dd>
<dt>Down payment</dt><dd id="dp"></dd>
<dt>Loan amount</dt><dd id="loan"></dd>
<dt>Monthly payment</dt><dd id="pmt"></dd>
<dt>Yearly debt payments</dt><dd id="ads"></dd>
<dt>Debt coverage ratio</dt><dd id="dscr"></dd>
<dt>Cash left after loan and your salary</dt><dd id="left"></dd>
<dt>Yearly return on down payment</dt><dd id="coc"></dd>
</dl>
</section>

<section class="card">
<h2>Save this deal</h2>
<div class="row"><input id="name" type="text" placeholder="Name it, like Tampa laundromat" maxlength="40"><button id="save">Save deal</button></div>
<div id="list"></div>
</section>

<section class="card">
<h2>What is a good SDE multiple?</h2>
<p>SDE (seller's discretionary earnings) is the profit a single owner-operator takes home, including their salary. Small businesses commonly sell for about 2x to 4x SDE. Lower multiples mean you pay less per dollar of cash flow. Higher ones need strong growth or very stable earnings to make sense.</p>
<h2>What DSCR do lenders want?</h2>
<p>The debt service coverage ratio (DSCR) is cash flow available for debt divided by yearly loan payments. SBA lenders typically want 1.25 or higher. Below 1.0, the business does not earn enough to cover the loan.</p>
<h2>How BuyCheck works</h2>
<p>BuyCheck subtracts the salary you need from the listing's cash flow, then compares what is left to your loan payments. The verdict is a rule of thumb to help you screen listings fast. Always verify the seller's books before making an offer.</p>
</section>

<p class="note">Estimates only, not financial advice. Verify the seller's numbers with a CPA and talk to a lender before making an offer. Saved deals stay in this browser on this device.</p>
</main>
<script>
const $=id=>document.getElementById(id);
const n=id=>parseFloat($(id).value)||0;
const usd=v=>(v<0?"-":"")+"$"+Math.round(Math.abs(v)).toLocaleString("en-US");
let cur={};
function calc(){
  const price=n("price"),rev=n("rev"),sde=n("sde"),sal=n("sal"),down=n("down")/100,rate=n("rate")/100,yrs=n("term");
  const dp=price*down,loan=price-dp,m=rate/12,k=yrs*12;
  const pmt=k<=0?0:(m===0?loan/k:loan*m/(1-Math.pow(1+m,-k)));
  const ads=pmt*12,avail=sde-sal,dscr=ads>0?avail/ads:0;
  const mult=sde>0?price/sde:0,left=avail-ads,coc=dp>0?left/dp:0;
  let cls,t,d;
  if(sde<=0||price<=0){cls="";t="Enter the numbers";d="Fill in price and cash flow to see a verdict."}
  else if(dscr>=1.5&&mult<=3.5){cls="good";t="Strong deal";d="Cash flow covers the loan with room to spare at a fair price. Verify the books."}
  else if(dscr>=1.25&&mult<=4.5){cls="warn";t="Decent, check the details";d="Lenders usually want 1.25 or higher. Confirm the numbers and try to negotiate the price."}
  else if(dscr>=1){cls="warn";t="Risky";d="It barely covers the loan. One bad month could hurt. Push for a lower price or more seller financing."}
  else{cls="bad";t="Pass, or renegotiate";d="Cash flow after your salary doesn't cover the loan payments at this price."}
  $("vcard").className="card verdict "+cls;$("vt").textContent=t;$("vd").textContent=d;
  $("pin").style.left=Math.max(0,Math.min(100,(dscr-0.8)/1.2*100))+"%";
  $("mult").textContent=mult?mult.toFixed(2)+"x":"n/a";
  $("rmult").textContent=rev>0?(price/rev).toFixed(2)+"x":"n/a";
  $("dp").textContent=usd(dp);$("loan").textContent=usd(loan);$("pmt").textContent=usd(pmt);$("ads").textContent=usd(ads);
  $("dscr").textContent=ads>0?dscr.toFixed(2):"n/a";$("left").textContent=usd(left);
  $("coc").textContent=dp>0?(coc*100).toFixed(0)+"%":"n/a";
  cur={price,sde,mult,dscr,t,cls,inputs:{price:n("price"),rev,sde,sal,down:n("down"),rate:n("rate"),term:yrs}};
}
let deals=[];
try{deals=JSON.parse(localStorage.getItem("deals")||"[]")}catch(e){deals=[]}
function store(){try{localStorage.setItem("deals",JSON.stringify(deals))}catch(e){}}
function render(){
  const el=$("list");el.textContent="";
  if(!deals.length){const p=document.createElement("p");p.className="note";p.textContent="No saved deals yet. Save one to compare it with the next listing.";el.appendChild(p);return}
  deals.slice().sort((a,b)=>b.dscr-a.dscr).forEach(d=>{
    const row=document.createElement("div");row.className="deal";
    const info=document.createElement("div");
    const b=document.createElement("b");b.textContent=d.name;
    const s=document.createElement("small");s.textContent=usd(d.price)+" · "+d.mult.toFixed(1)+"x · coverage "+d.dscr.toFixed(2)+" · "+d.t;
    info.append(b,s);
    const load=document.createElement("button");load.className="x";load.textContent="Load";
    load.onclick=()=>{Object.keys(d.inputs).forEach(k=>$(k).value=d.inputs[k]);calc();scrollTo({top:0,behavior:"smooth"})};
    const del=document.createElement("button");del.className="x";del.textContent="Delete";
    del.onclick=()=>{deals=deals.filter(x=>x!==d);store();render()};
    const act=document.createElement("div");act.className="row";act.append(load,del);
    row.append(info,act);el.appendChild(row);
  });
}
document.querySelectorAll("input").forEach(i=>i.addEventListener("input",calc));
$("save").onclick=()=>{
  const nm=$("name").value.trim()||"Deal "+(deals.length+1);
  deals.push({name:nm,price:cur.price,mult:cur.mult,dscr:cur.dscr,t:cur.t,inputs:cur.inputs});
  store();render();$("name").value="";
};
calc();render();
</script>
</body>
</html>
