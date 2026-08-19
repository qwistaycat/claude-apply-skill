# ATS recipes

## Two rules that outrank everything else

**Real keystrokes only reach the Chrome-ACTIVE tab.** Even inside `browser_batch` with an
explicit `tabId`, `computer type` lands in whatever tab Chrome has fronted. Call
`mcp__Control_Chrome__switch_to_tab {tab_id: N}` (standalone, snake_case) before every
typing session on a new tab. Skipping this produced a whole fill pass that read back empty.

**Inside `browser_batch`, sub-action names are un-namespaced**: `navigate`, `computer`,
`javascript_tool`, `tabs_create_mcp`. Passing the `mcp__claude-in-chrome__` prefix fails
with `unknown tool`.

Related: `computer` and `navigate` called standalone ignore `tabId` and act on the active
tab, which once navigated a filled form away and destroyed it. `javascript_tool`,
`read_page`, `find`, and `file_upload` do honour `tabId`. To move a specific tab, run
`location.href = ...` inside it. `computer wait` maxes at 10s, `key ... repeat` at 100.

## Scrolling

`scrollIntoView({block:'center'})` silently does nothing on Greenhouse and Pinpoint.

```js
window.__fid=function(id){var e=document.getElementById(id);if(!e)return 'NOEL';window.scrollTo(0,window.scrollY+e.getBoundingClientRect().top-300);e.focus();return document.activeElement===e};
```

## Revealing a hidden file input

```js
document.querySelectorAll('input[type=file]').forEach(function(f,i){f.style.cssText='position:fixed;top:0;left:0;width:200px;height:40px;opacity:1;z-index:99999';f.setAttribute('aria-label','REALFILEINPUT'+i)});
```

Then `find` the label and call `file_upload` as a standalone call. `find` and `read_page`
**do not traverse into iframes**, so for iframe-based ATSs use the proxy trick under iCIMS.

---

## Ashby

JS injection is rejected by its form store. Native-setter `value` plus events,
`button.click()`, and `execCommand('insertText')` all leave fields "Missing entry".

Text fields: `focus`, then real `key End`, `key Backspace repeat:40`, `type`.
Yes/No buttons: screenshot and read the coordinate visually. `getBoundingClientRect` math
picked the wrong fieldset twice. Submit is the exception, `.click()` works.

Its submit-time error list is the only trustworthy validator. Fix only what it names.

## Greenhouse

react-select comboboxes: `__fid(id)`, `type "<filter>"`, `wait 1`, `key Return`. Verify by
walking up to 6 parents and reading `innerText`; the input's own `.value` stays empty.

The field with id `country` is the **phone country-code** selector, not a location field.
Its "blank required field" report is a false positive.

Tab discarding under memory pressure loses everything on Greenhouse. Do not leave a filled
Greenhouse form parked while you work elsewhere.

## Lever and Gem

Survive tab discarding except for file attachments. If a tab was discarded, re-attach the
resume before submitting.

## Workable and Phenom

Workable can survive tab discarding fully. Phenom resets to step 1.

## Pinpoint (`*.pinpointhq.com`)

Each combobox is a visible text input over a `display:none` native `<select>`. `read_page`
only returns viewport-visible elements, so refs for off-screen fields do not exist.

The dropdown has **no typeahead filtering**. Typing a word and pressing Return selects
whatever was highlighted first, which once quietly picked "Intersex" for a typed "Female".
Correct recipe: real `left_click` the box, screenshot, then `left_click` the option by its
pixel position.

Country, state, and screener questions sync the native select. The **Diversity and
Inclusion widgets do not** (the select stays `value="blank"`, the real answer lives in a
hidden `application_form[equality_monitoring_options][]` id), so verify those by the
widget's rendered `innerText`.

The final "Allow us to process your personal information" checkbox looks like optional
marketing consent but is a **hard gate** on submission. Its real `<input>` is
`display:none`; click the visible proxy and re-query to confirm `checked`.

## Avature (`*.avature.net`)

Four steps: Your Information / Questions / Voluntary Self-Identification / Sign & Submit.

**Never navigate backwards.** The progress-bar circles are decorative and not clickable,
and `Previous` from Questions jumps all the way back to JobDetail, which **wipes every
answer on Questions and Self-Identification**. Step 1's uploaded resume survives and the
circles keep showing checkmarks, so the loss is invisible until you re-audit.

`<select>` elements are lazily populated (`options.length === 1`) until the widget is
clicked once, and clicking renders no visible option list here. So: real `left_click` the
box, then set it in JS by `selectedIndex` plus `input`/`change` dispatch.

Radios respond to plain `.click()`. Fastest way through a long screener: group
`input[type=radio]` by the first line of `closest('fieldset').innerText`, then click the
option whose `<label>` text matches the intended answer.

`careers/MyApplications` confirms "Application is complete." but never shows which resume
file was attached.

## iCIMS (`*.icims.com`)

**Always a blocker.** iCIMS requires creating a login (password twice) plus an hCaptcha
before Submit Profile. Fill everything else, leave the tab open, report it.

Everything lives inside `#icims_content_iframe`. `find` and `read_page` cannot see into it.

**Upload the resume FIRST.** Attaching re-parses the PDF and re-renders the entire form,
wiping address, zip, education, every screener dropdown, and rebuilding the work-experience
blocks from the PDF text. Fill order: resume, then everything else.

Resume upload. The form is `multipart/form-data` and the file input has an `onchange`
handler, so you **must** dispatch `change`:

```js
// 1. proxy input in the TOP document
var p=document.createElement('input');p.type='file';p.id='PROXYFI';
p.setAttribute('aria-label','REALFILEINPUT0');
p.style.cssText='position:fixed;top:10px;left:10px;width:280px;height:40px;opacity:1;z-index:2147483647;background:#ff0';
document.body.appendChild(p);
// 2. find -> file_upload to that ref (standalone call, param is `paths`)
// 3. transfer using the IFRAME's own realm
var fr=document.getElementById('icims_content_iframe');var w=fr.contentWindow,d=fr.contentDocument;
var f=d.querySelector('input[type=file]');
var dt=new w.DataTransfer();dt.items.add(p.files[0]);f.files=dt.files;
f.dispatchEvent(new w.Event('input',{bubbles:true}));
f.dispatchEvent(new w.Event('change',{bubbles:true}));
```

Skipping `change` leaves the file present client-side but the server rejects with
"Please upload your resume (Max size: 5 MB): required."

Dropdowns. The native `<select>` stays `options.length === 1` forever, so JS value
assignment can never work. The option rows are **pre-rendered in the DOM even while
closed**, as `li.dropdown-result` inside `ul.dropdown-results` in the same parent as the
select. Click the row directly, no need to open the widget:

```js
function pick(lbl,rowText){var d=document.getElementById('icims_content_iframe').contentDocument;
var e=null;d.querySelectorAll('label').forEach(function(l){if(e)return;
  if(l.innerText.trim().indexOf(lbl)===0){var x=d.getElementById(l.getAttribute('for'));if(x)e=x;}});
if(!e)return 'MISS';
var lis=e.parentElement.querySelectorAll('ul.dropdown-results li');
for(var i=0;i<lis.length;i++){if(lis[i].innerText.trim()===rowText){lis[i].click();return e.value;}}
return 'NOROW';}
```

Searchable dropdowns (School, Major) load only 26 rows and filter **only on real
keystrokes**. Synthetic `input`, `keyup`, and even a real `KeyboardEvent` do not filter.
So: `switch_to_tab`, real `left_click` the box, real `type` the query, screenshot, then
click the matching row.

Plain inputs, textareas, and true native selects accept direct `.value` assignment plus
`input`/`change` dispatch from the iframe document.

Empty-required audit, run before every submit:

```js
(function(){var d=document.getElementById('icims_content_iframe').contentDocument;var out=[];
d.querySelectorAll('input,select,textarea').forEach(function(e){
  if(e.type==='hidden'||e.type==='file')return;
  var req=e.required||e.getAttribute('aria-required')==='true';if(!req)return;
  var v=(e.value||'').trim();
  if(v===''||v==='0'){var l=d.querySelector('label[for="'+e.id+'"]');
    out.push((l?l.innerText.trim().slice(0,34):e.id));}});
return 'EMPTY REQUIRED >> '+(out.join(' ; ')||'none');})()
```

A clean iCIMS form ends with exactly three entries left: Password, Password (Re-enter),
and one unlabeled text input (the hCaptcha).

Cross-iframe coordinate helper, for the cases that need a real click. Note that
`scrollIntoView` inside the iframe does not scroll the outer page, so scroll the top
document. `K` is the screenshot-to-CSS scale factor; recompute it if the window resizes
(screenshots came back 1568x767 against a 1728x845 viewport, so `K = 0.9074`).

```js
window.__go=function(txt){var f=document.getElementById('icims_content_iframe');var d=f.contentDocument;var el=null;
d.querySelectorAll('label').forEach(function(l){if(el)return;if(l.innerText.trim().indexOf(txt)===0){var e=d.getElementById(l.getAttribute('for'));if(e)el=e;}});
if(!el)return 'MISS';
var box=el.parentElement.querySelector('.select2-container')||el.nextElementSibling||el;
var fr=f.getBoundingClientRect();var r=box.getBoundingClientRect();
window.scrollTo(0,window.scrollY+fr.top+r.top-350);
fr=f.getBoundingClientRect();r=box.getBoundingClientRect();
var K=0.9074;return Math.round((fr.left+r.left+r.width/2)*K)+' , '+Math.round((fr.top+r.top+r.height/2)*K);};
```

## Workday

Almost always requires creating an account. Treat like iCIMS: fill what you can, then
blocked. If the user already has a login for that tenant, the session usually persists.

## Indeed

`smartapply.indeed.com/beta/indeedapply/form/...` URLs are the resumed state of an
"Apply with Indeed" flow on some job, not separate postings. The resume step defaults to a
Beta "AI-tailored Indeed Resume"; uploading a file through the page's single
`input[type=file]` replaces it and auto-selects the upload. A `Current CTC` screener means
an India-based employer, so skip it.

## The Simplify extension

If the Simplify extension is installed, its panel toggles on and off between pages and shifts the
content column's x-center (around 670 with the panel, around 800 or 585 without). Always
screenshot immediately before clicking by coordinate.
