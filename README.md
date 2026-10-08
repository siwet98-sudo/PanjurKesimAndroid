# PanjurKesimAndroid
<!doctype html>
<html lang="tr">
<head>
<meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1"><meta name="theme-color" content="#172033">
<title>Panjur Kesim Hesaplama</title>
<style>
*{box-sizing:border-box}body{margin:0;background:#f2f5f9;color:#172033;font-family:system-ui,-apple-system,Segoe UI,Roboto,sans-serif}main{max-width:650px;margin:auto;padding:16px 13px 30px}.head{display:flex;gap:12px;align-items:center;margin:5px 0 18px}.logo{height:50px;width:50px;display:grid;place-items:center;background:#172033;color:white;border-radius:15px;font-size:24px}h1{font-size:22px;margin:0 0 3px}.sub,.muted{font-size:13px;color:#667085}.card{background:white;border:1px solid #dce3eb;border-radius:17px;padding:16px;margin:12px 0;box-shadow:0 3px 12px #10182808}label{display:block;font-size:14px;font-weight:650;margin:12px 0 7px}input,select{width:100%;font:inherit;padding:12px;border:1px solid #cbd5e1;border-radius:11px;background:white;color:#172033}.grid{display:grid;grid-template-columns:1fr 1fr;gap:12px}.units{display:flex;gap:14px;align-items:center;flex-wrap:wrap;margin-top:14px}.units label{font-weight:500;margin:0;display:flex;align-items:center;gap:6px}.units input{width:auto}.primary,.secondary{width:100%;padding:13px;border:0;border-radius:11px;font:inherit;font-weight:700;margin-top:13px;cursor:pointer}.primary{background:#172033;color:white}.secondary{background:#e8edf4;color:#172033}.results{display:grid;grid-template-columns:1fr 1fr;gap:9px}.result{border:1px solid #dce3eb;border-radius:12px;padding:12px;min-width:0}.name{font-size:13px;color:#667085}.value{font-size:21px;font-weight:750;overflow-wrap:anywhere;margin-top:5px}.calc{font-size:12px;color:#667085;margin-top:4px}.hero{background:#eaf0f6;border-radius:12px;padding:13px;margin-bottom:12px}.hero small{display:block;color:#667085}.hero strong{font-size:20px}.note{font-size:12px;color:#667085;line-height:1.55}.sectionTitle{font-weight:750;font-size:16px;margin-bottom:3px}.hide{display:none}@media(max-width:380px){.value{font-size:18px}.card{padding:13px}}
</style></head>
<body><main>
<header class="head"><div class="logo">📐</div><div><h1>Panjur Kesim Hesaplama</h1><div class="sub">SL77 ve SL56 modelleri tek uygulamada</div></div></header>
<section class="card">
 <label for="model">Profil modeli</label><select id="model"><option value="SL77">SL77</option><option value="SL56">SL56</option></select>
 <label for="box">Kutu ölçüsü</label><select id="box"></select>
 <div class="grid"><div><label for="en">En</label><input id="en" type="number" min="0" step="any" inputmode="decimal" value="400"></div><div><label for="boy">Boy</label><input id="boy" type="number" min="0" step="any" inputmode="decimal" value="290"></div></div>
 <div class="units"><b>Birim:</b><label><input type="radio" name="unit" value="cm" checked> cm</label><label><input type="radio" name="unit" value="mm"> mm</label><label><input type="radio" name="unit" value="m"> metre</label></div>
 <button class="primary" id="calcBtn">Hesapla</button>
</section>
<section class="card" aria-live="polite">
 <div class="hero"><small>Girilen ölçü</small><strong id="dimensions">400 × 290 cm</strong></div>
 <div class="results">
  <div class="result"><div class="name">Dikme boy</div><div class="value" id="dikme">378 cm</div><div class="calc" id="dikmeCalc"></div></div>
  <div class="result"><div class="name">Lamel adedi</div><div class="value" id="adet">70 adet</div><div class="calc" id="adetCalc"></div></div>
  <div class="result"><div class="name">Saç</div><div class="value" id="sac">289 cm</div><div class="calc" id="sacCalc"></div></div>
  <div class="result"><div class="name">Bora</div><div class="value" id="bora">283,5 cm</div><div class="calc" id="boraCalc"></div></div>
  <div class="result" style="grid-column:1/-1"><div class="name">Lamel en</div><div class="value" id="lamelBoy">280 cm</div><div class="calc" id="lamelBoyCalc"></div></div>
 </div>
 <button class="secondary" id="copyBtn">Kesim listesini kopyala</button><button class="secondary" id="shareBtn">Paylaş</button>
</section>
<p class="note" id="rules"></p>
</main>
<script>
const $=id=>document.getElementById(id);
const fmt=n=>String(Number(n.toFixed(2))).replace('.',',');
function updateBoxes(){
 const model=$('model').value, old=$('box').value, boxes=model==='SL56'?[25,20,18]:[30,35];
 $('box').innerHTML=boxes.map(n=>`<option value="${n}">${n}’lik kutu</option>`).join('');
 if(boxes.includes(Number(old)))$('box').value=old;
}
function calculate(){
 const enRaw=$('en').value,boyRaw=$('boy').value;
 if(!enRaw.trim()||!boyRaw.trim())return;
 const model=$('model').value,box=Number($('box').value),unit=document.querySelector('input[name="unit"]:checked').value;
 const factor=unit==='m'?100:unit==='mm'?0.1:1,en=Number(enRaw)*factor,boy=Number(boyRaw)*factor;
 if(!Number.isFinite(en)||!Number.isFinite(boy)||en<=0||boy<=0)return;
 let dikme,adet,sac,bora,lamelBoy;
 if(model==='SL56'){dikme=boy-box+3;adet=Math.ceil(dikme/5.6)+2;sac=en-1;bora=en-6.5;lamelBoy=en-10;
 $('rules').textContent='SL56: Dikme boyu = Boy − kutu ölçüsü + 3 cm. Lamel adedi = dikme ÷ 5,6 yukarı yuvarla + 2. Saç = En − 1 cm. Bora = En − 6,5 cm. Lamel eni = En − 10 cm.';}
 else{dikme=boy-box+3;adet=Math.ceil(dikme/7.7)+3;sac=en-1;bora=en-9;lamelBoy=en-13;
 $('rules').textContent='SL77: Dikme boyu = Boy − kutu ölçüsü + 3 cm. Lamel adedi = dikme ÷ 7,7 yukarı yuvarla + 3. Saç = En − 1 cm. Bora = En − 9 cm. Lamel eni = En − 13 cm.';}
 const out=n=>fmt(n/factor), dim=`${fmt(Number(enRaw))} × ${fmt(Number(boyRaw))} ${unit}`;
 $('dimensions').textContent=dim;$('dikme').textContent=out(dikme)+' '+unit;$('dikmeCalc').textContent=`${fmt(Number(boyRaw))} − ${fmt(box/factor)} + ${fmt(3/factor)}`;
 $('adet').textContent=adet+' adet';$('adetCalc').textContent=`Dikme ÷ ${fmt((model==='SL56'?5.6:7.7)/factor)} yukarı yuvarla + ${model==='SL56'?2:3}`;
 $('sac').textContent=out(sac)+' '+unit;$('sacCalc').textContent=`${fmt(Number(enRaw))} − ${fmt(1/factor)}`;
 $('bora').textContent=out(bora)+' '+unit;$('boraCalc').textContent=`${fmt(Number(enRaw))} − ${fmt((model==='SL56'?6.5:9)/factor)}`;
 $('lamelBoy').textContent=out(lamelBoy)+' '+unit;$('lamelBoyCalc').textContent=`${fmt(Number(enRaw))} − ${fmt((model==='SL56'?10:13)/factor)}`;
}
function list(){return `KESİM LİSTESİ\nModel: ${$('model').value}\nKutu: ${$('box').value}’lik\nÖlçü: ${$('dimensions').textContent}\nDikme: ${$('dikme').textContent}\nLamel: ${$('adet').textContent}\nSaç: ${$('sac').textContent}\nBora: ${$('bora').textContent}\nLamel en: ${$('lamelBoy').textContent}`;}
$('model').addEventListener('change',()=>{updateBoxes();calculate()});$('box').addEventListener('change',calculate);
['en','boy'].forEach(id=>$(id).addEventListener('input',calculate));document.querySelectorAll('input[name="unit"]').forEach(el=>el.addEventListener('change',calculate));$('calcBtn').addEventListener('click',calculate);
$('copyBtn').addEventListener('click',async()=>{try{await navigator.clipboard.writeText(list());alert('Kesim listesi kopyalandı.')}catch(e){alert(list())}});
$('shareBtn').addEventListener('click',async()=>{if(navigator.share){try{await navigator.share({title:'Kesim Listesi',text:list()})}catch(e){}}else{try{await navigator.clipboard.writeText(list());alert('Liste kopyalandı.')}catch(e){alert(list())}}});
updateBoxes();calculate();
</script></body></html>
