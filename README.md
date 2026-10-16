# task.fiber<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>مختبر تشخيص خسائر الألياف البصرية</title>
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;800&display=swap">
<style>
:root{--bg:#f4f6fb;--surface:#fff;--ink:#0b1b3a;--muted:#55627f;--line:#d5dcec;--accent:#e8731f;--ok:#1b8a52;--bad:#c4372f;--blue:#2f5fc0;box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--bg:#071a3a;--surface:#102a55;--ink:#f2f5fb;--muted:#a9b6d3;--line:#2a4678;--accent:#ff8a3d;--ok:#4cd08a;--bad:#ff7a70;--blue:#7ea4f0}}
:root[data-theme="dark"]{--bg:#071a3a;--surface:#102a55;--ink:#f2f5fb;--muted:#a9b6d3;--line:#2a4678;--accent:#ff8a3d;--ok:#4cd08a;--bad:#ff7a70;--blue:#7ea4f0}
*{box-sizing:inherit}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
body{margin:0;background:var(--bg);color:var(--ink);font-family:"Cairo",system-ui,sans-serif;line-height:1.7}
main{max-width:860px;margin:0 auto;padding:20px 16px 48px}
h1{font-size:1.8rem;font-weight:800;margin:8px 0 2px}
.sub{color:var(--accent);font-weight:600;direction:ltr;text-align:right;margin:0 0 16px}
.bar{display:flex;gap:8px;margin:12px 0 20px}
.bar span{flex:1;height:8px;border-radius:4px;background:var(--line)}
.bar span.on{background:var(--accent)}
.card{background:var(--surface);border:1px solid var(--line);border-radius:14px;padding:18px}
h2{margin:0 0 4px;font-size:1.25rem}
p{margin:0 0 12px;color:var(--muted)}
.cols{display:grid;grid-template-columns:1fr 1fr;gap:12px}
@media(max-width:560px){.cols{grid-template-columns:1fr}}
.col{display:flex;flex-direction:column;gap:8px}
button{font:inherit;color:inherit;cursor:pointer}
.opt{background:var(--bg);border:2px solid var(--line);border-radius:10px;padding:10px 12px;text-align:right;min-height:48px;transition:all 0.2s ease}
.opt:hover{border-color:var(--blue)}
.opt:focus-visible,.btn:focus-visible{outline:3px solid var(--accent);outline-offset:2px}
.opt.sel{border-color:var(--accent)}
.opt.done{border-color:var(--ok);background:color-mix(in srgb,var(--ok) 16%,var(--surface));cursor:default}
.opt.wrong{border-color:var(--bad);animation:sh .3s}
@keyframes sh{25%{transform:translateX(-5px)}75%{transform:translateX(5px)}}
@media(prefers-reduced-motion:reduce){.opt.wrong{animation:none}}
.en{display:block;direction:ltr;text-align:right;font-size:.8rem;color:var(--muted)}
.btn{background:var(--accent);color:#111;border:0;border-radius:10px;padding:10px 22px;font-weight:800;margin-top:16px;transition:opacity 0.2s}
.btn[disabled]{opacity:.4;cursor:not-allowed}
.fb{margin-top:12px;padding:10px 12px;border-radius:10px;min-height:1px}
.fb.ok{background:color-mix(in srgb,var(--ok) 16%,var(--surface))}
.fb.no{background:color-mix(in srgb,var(--bad) 16%,var(--surface))}
.scn{font-size:1.1rem;color:var(--ink);font-weight:600}
.meta{display:flex;justify-content:space-between;color:var(--muted);font-size:.9rem;margin-bottom:8px}
.big{font-size:2.4rem;font-weight:800;color:var(--accent);margin:0}
</style>
</head>
<body>
<main>
<h1>مختبر تشخيص خسائر الألياف البصرية</h1>
<p class="sub">Losses in Optical Fiber Cable — Diagnosis Lab</p>
<div class="bar" id="bar" aria-hidden="true"><span></span><span></span><span></span></div>
<section class="card" id="stage" aria-live="polite"></section>
</main>
<script>
const L=[
{id:'abs',ar:'الامتصاص',en:'Absorption Losses',cause:'شوائب في مادة الليف'},
{id:'rad',ar:'خسائر الإشعاع والانحناء',en:'Radiation Losses',cause:'انحناءات والتواءات في الكابل'},
{id:'ray',ar:'التشتت المادي (رالي)',en:'Rayleigh Scattering',cause:'عدم انتظام مجهري في المادة'},
{id:'mod',ar:'التشتت النمطي',en:'Modal Dispersion',cause:'سير الأشعة في مسارات مختلفة'},
{id:'chr',ar:'التشتت اللوني',en:'Chromatic Dispersion',cause:'مصدر ضوئي غير أحادي اللون'},
{id:'cou',ar:'خسائر الاقتران والتوصيل',en:'Coupling Losses',cause:'عدم محاذاة وسوء تشطيب الأسطح'}];

const S=[
{t:'ثنى فني الكابل بزاوية حادة خلف الرف، فانخفضت قوة الإشارة عند هذه النقطة.',a:'rad',w:['abs','cou'],why:'الانحناء الحاد يسرّب الضوء خارج القلب: خسائر إشعاع.'},
{t:'في ليف متعدد الأنماط وعلى مسافة طويلة، تتداخل النبضات المتتابعة وتصبح غير مفهومة.',a:'mod',w:['chr','ray'],why:'الأشعة تقطع مسارات مختلفة فتصل في أزمنة مختلفة: اتساع النبضة.'},
{t:'وصلة جديدة تفقد جزءاً كبيراً من الإشارة، وطرف الليف يبدو غير مستوٍ تحت المجهر.',a:'cou',w:['abs','rad'],why:'سوء تشطيب السطح وعدم المحاذاة من أسباب خسائر الاقتران.'},
{t:'ليف منخفض الجودة تحتوي مادته على شوائب معدنية تمتص جزءاً من الضوء.',a:'abs',w:['ray','mod'],why:'الشوائب تمتص طاقة الضوء وتحوّلها إلى حرارة: خسائر امتصاص.'},
{t:'استُخدم مصدر ضوئي عريض الأطوال الموجية فاتسعت النبضة، لأن كل لون ينتقل بسرعة مختلفة.',a:'chr',w:['mod','cou'],why:'المصدر غير أحادي اللون يسبب التشتت اللوني.'},
{t:'حتى في ليف مثالي وبلا انحناءات يوجد ضعف طفيف ناتج عن تشتت الضوء بسبب عدم الانتظام الجزيئي.',a:'ray',w:['abs','mod'],why:'التشتت المادي (رالي) ناتج عن التغيرات المجهرية في كثافة مادة الزجاج.'}
];

let cur = 0, score = 0, done = false;

function renderBar() {
  const bar = document.getElementById('bar');
  bar.innerHTML = S.map((_, i) => `<span class="${i <= cur ? 'on' : ''}"></span>`).join('');
}

function render() {
  renderBar();
  const stage = document.getElementById('stage');
  if (cur >= S.length) {
    stage.innerHTML = `
      <h2>أتممت المختبر بنجاح!</h2>
      <p class="sub">Lab Completed Successfully</p>
      <p>لقد أنهيت جميع سيناريوهات تشخيص الخسائر في الألياف البصرية.</p>
      <p class="big">${score} / ${S.length}</p>
      <button class="btn" onclick="restart()">إعادة المختبر</button>
    `;
    return;
  }
  const s = S[cur];
  const options = [s.a, ...s.w].sort(() => Math.random() - 0.5);
  
  stage.innerHTML = `
    <div class="meta">
      <span>سيناريو ${cur + 1} من ${S.length}</span>
      <span>النتيجة: ${score}</span>
    </div>
    <h2>حالة تشخيصية</h2>
    <p class="scn">${s.t}</p>
    <div class="cols" id="options">
      ${options.map(id => {
        const item = L.find(x => x.id === id);
        return `<button class="opt" data-id="${id}">${item.ar}<span class="en">${item.en}</span></button>`;
      }).join('')}
    </div>
    <div class="fb" id="fb"></div>
    <button class="btn" id="nextBtn" disabled>السؤال التالي</button>
  `;

  const optButtons = stage.querySelectorAll('.opt');
  const nextBtn = document.getElementById('nextBtn');
  const fb = document.getElementById('fb');

  optButtons.forEach(btn => {
    btn.addEventListener('click', () => {
      if (done) return;
      const chosenId = btn.getAttribute('data-id');
      done = true;
      if (chosenId === s.a) {
        btn.classList.add('done');
        fb.className = 'fb ok';
        fb.innerHTML = `<strong>إجابة صحيحة!</strong> ${s.why}`;
        score++;
      } else {
        btn.classList.add('wrong');
        optButtons.forEach(b => {
          if (b.getAttribute('data-id') === s.a) b.classList.add('done');
        });
        fb.className = 'fb no';
        fb.innerHTML = `<strong>إجابة خاطئة.</strong> ${s.why}`;
      }
      nextBtn.removeAttribute('disabled');
    });
  });

  nextBtn.addEventListener('click', () => {
    cur++;
    done = false;
    render();
  });
}

function restart() {
  cur = 0;
  score = 0;
  done = false;
  render();
}

render();
</script>
</body>
</html>
