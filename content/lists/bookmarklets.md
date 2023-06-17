---
title: Bookmarklets
summary: "Bookmarklets Collection."
bookToc: true
draft: true
---

# Bookmarklets

## Font Finder
View Fonts used.

```js
javascript:(function(){function getSelectedNode(){if(window.getSelection().focusNode===null)return null;return window.getSelection().focusNode.parentNode}function getNodeFontStack(node){return window.getComputedStyle(node).fontFamily}function getFirstAvailableFont(fonts){for(let font of fonts){let fontName=font.trim().replace(/"/g,'');let isAvailable=document.fonts.check(`16px ${fontName}`);if(!isAvailable)continue;return fontName}}let node=getSelectedNode();if(!node){window.alert('Please select a string of text and try again.');return}let fonts=getNodeFontStack(node).split(',');let firstAvailableFont=getFirstAvailableFont(fonts);window.alert(`Font: ${firstAvailableFont}`)}())
```

## Calculator
Displays 

```js
javascript:var evl,em,expr=prompt('Formula... (eg: 2*3 + 7/8)','');with(Math)try{evl=  parseFloat(eval(expr));if(isNaN(evl)) {throw Error('Not a number!');}void(prompt ('Result:'  ,evl));}catch(em){alert(em);}
```

## Stats

```js
javascript:location.href='http://cssstats.com/stats?url=%27+window.location.href
```

## Stress Test

```js
javascript:(function()%7Bvar%20d=document,s=d.createElement('script'),doit=function()%7Bif(window.stressTest)%7BstressTest.bookmarklet();%7Delse%7BsetTimeout(doit,100);%7D%7D;s.src='https://rawgithub.com/andyedinborough/stress-css/master/stressTest.js?_='%2BMath.random();(d.body%7C%7Cd.getElementsByTagName('head')%5B0%5D).appendChild(s);doit();%7D)();
```

## Tab Cloak - Edit Tab Title and Icon

```js
javascript:document.title=prompt('Welcome to the Tab Cloak setup!\n\nEnter the title you want to set for this tab::');var icon=document.querySelector(`link[rel='icon']`);if (!icon) {icon = document.createElement('link');icon.rel='icon';};switch(prompt('What icon would you like to use?\n\n[1] Google Search\n[2] Google Drive\n[3] Custom URL\n\nPlease only enter a number!%27)){case%271%27:icon.setAttribute(%27href%27,%27https://www.google.com/favicon.ico%27);break;case%272%27:icon.setAttribute(%27href%27,%27https://ssl.gstatic.com/images/branding/product/1x/drive_2020q4_32dp.png%27);break;case%273%27:icon.setAttribute(%27href%27,prompt(%27Please enter the URL for the icon you want:%27));} document.head.appendChild(icon);
```

## Tab Title Editor

```js
javascript:void(document.title=prompt('Enter page title') ?? document.title)
```

## Find Text and Highlight It

```js
javascript:(function(){var count=0, text, dv;text=prompt("Search phrase:", "");if(text==null || text.length==0)return;hlColor=prompt("Color:", "yellow");dv=document.defaultView;function searchWithinNode(node, te, len){var pos, skip, spannode, middlebit, endbit, middleclone;skip=0;if( node.nodeType==3 ){pos=node.data.toUpperCase().indexOf(te);if(pos>=0){spannode=document.createElement("SPAN");spannode.style.backgroundColor= hlColor;middlebit=node.splitText(pos);endbit=middlebit.splitText(len);middleclone=middlebit.cloneNode(true);spannode.appendChild(middleclone);middlebit.parentNode.replaceChild(spannode,middlebit);++count;skip=1;}}else if( node.nodeType==1&& node.childNodes && node.tagName.toUpperCase()!="SCRIPT" && node.tagName.toUpperCase!="STYLE"){for (var child=0; child < node.childNodes.length; ++child){child=child+searchWithinNode(node.childNodes[child], te, len);}}return skip;}window.status="Searching for '"+text+"'...";searchWithinNode(document.body, text.toUpperCase(), text.length);window.status="Found "+count+" occurrence"+(count==1?"":"s")+" of %27"+text+"%27.";})();
```

## Quick Edit
Edit elements on a website live.

```js
javascript:(function(){  document.designMode='on';  const s=document.createElement('style');  s.innerHTML=`body::before{content:'%E2%9C%8F%EF%B8%8F Edit Mode (ESC to end)';z-index:64;padding:1em;background:white;color:black;display:block;margin:1em;font-size:30px;border:5px solid green;}`;  document.body.appendChild(s);  window.scrollTo(0,0);  document.addEventListener('keyup',e => {    if(e.key==='Escape'){      document.designMode='off';      s.remove();      document.removeEventListener('keyup',e);    }  });})();
```

## URL to QR Code

```js
javascript: void(() => {    open('https://chart.apis.google.com/chart?cht=qr&chs=300x300&chld=L|2&chl=%27 + (prompt(%27Enter text for QR code:%27) ?? (function() {        throw null;    }())), null, %27location=no,status=yes,menubar=no,scrollbars=no,resizable=yes,width=500,height=500,modal=yes,dependent=yes%27)})();
```

## User Agent Stats

```js
javascript: void(() => {prompt('User agent:', navigator.userAgent)})()
```

## View Fonts

```js
javascript: void((function(d) {     var e = d.createElement('script');     e.setAttribute('type', 'text/javascript');     e.setAttribute('charset', 'UTF-8');     e.setAttribute('src', '//www.typesample.com/assets/typesample.js?r=' + Math.random() * 99999999);     d.body.appendChild(e) })(document));
```

### Waterize Page

<a href="javascript:(function()%7B%2F%2F%20Water.css%20Bookmarklet%0A%2F%2F%20---------------------%0A%0Aconst%20%24%24%20%3D%20(selector)%20%3D%3E%20document.querySelectorAll(selector)%0Aconst%20createElement%20%3D%20(tagName%2C%20properties)%20%3D%3E%20Object.assign(document.createElement(tagName)%2C%20properties)%0A%0A%2F%2F%20Remove%20all%20CSS%20stylesheets%2C%20external%20and%20internal%0A%24%24('link%5Brel%3D%22stylesheet%22%5D%2Cstyle').forEach((el)%20%3D%3E%20el.remove())%0A%0A%2F%2F%20Remove%20all%20inline%20styles%0A%24%24('*').forEach((el)%20%3D%3E%20(el.style%20%3D%20''))%0A%0Aconst%20linkElm%20%3D%20createElement('link'%2C%20%7B%0A%20%20rel%3A%20'stylesheet'%2C%0A%20%20href%3A%20'https%3A%2F%2Fcdn.jsdelivr.net%2Fnpm%2Fwater.css%402%2Fout%2Flight.css'%0A%7D)%0A%0A%2F%2F%20Add%20water.css%20and%20responsive%20viewport%20(if%20necessary)%0Adocument.head.append(%0A%20%20linkElm%2C%0A%20%20!%24%24('meta%5Bname%3D%22viewport%22%5D').length%20%26%26%20createElement('meta'%2C%20%7B%0A%20%20%20%20name%3A%20'viewport'%2C%0A%20%20%20%20content%3A%20'width%3Ddevice-width%2Cinitial-scale%3D1.0'%0A%20%20%7D)%0A)%0A%0A%2F%2F%20Theme%20switching%20icons%0Aconst%20moonSVG%20%3D%20'%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20width%3D%2224%22%20height%3D%2224%22%20viewBox%3D%220%200%2024%2024%22%20fill%3D%22none%22%20stroke%3D%22currentColor%22%20stroke-width%3D%222%22%20stroke-linecap%3D%22round%22%20stroke-linejoin%3D%22round%22%20class%3D%22feather%20feather-moon%22%3E%3Cpath%20d%3D%22M21%2012.79A9%209%200%201%201%2011.21%203%207%207%200%200%200%2021%2012.79z%22%3E%3C%2Fpath%3E%3C%2Fsvg%3E'%0Aconst%20sunSVG%20%3D%20'%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20width%3D%2224%22%20height%3D%2224%22%20viewBox%3D%220%200%2024%2024%22%20fill%3D%22none%22%20stroke%3D%22currentColor%22%20stroke-width%3D%222%22%20stroke-linecap%3D%22round%22%20stroke-linejoin%3D%22round%22%20class%3D%22feather%20feather-sun%22%3E%3Ccircle%20cx%3D%2212%22%20cy%3D%2212%22%20r%3D%225%22%3E%3C%2Fcircle%3E%3Cline%20x1%3D%2212%22%20y1%3D%221%22%20x2%3D%2212%22%20y2%3D%223%22%3E%3C%2Fline%3E%3Cline%20x1%3D%2212%22%20y1%3D%2221%22%20x2%3D%2212%22%20y2%3D%2223%22%3E%3C%2Fline%3E%3Cline%20x1%3D%224.22%22%20y1%3D%224.22%22%20x2%3D%225.64%22%20y2%3D%225.64%22%3E%3C%2Fline%3E%3Cline%20x1%3D%2218.36%22%20y1%3D%2218.36%22%20x2%3D%2219.78%22%20y2%3D%2219.78%22%3E%3C%2Fline%3E%3Cline%20x1%3D%221%22%20y1%3D%2212%22%20x2%3D%223%22%20y2%3D%2212%22%3E%3C%2Fline%3E%3Cline%20x1%3D%2221%22%20y1%3D%2212%22%20x2%3D%2223%22%20y2%3D%2212%22%3E%3C%2Fline%3E%3Cline%20x1%3D%224.22%22%20y1%3D%2219.78%22%20x2%3D%225.64%22%20y2%3D%2218.36%22%3E%3C%2Fline%3E%3Cline%20x1%3D%2218.36%22%20y1%3D%225.64%22%20x2%3D%2219.78%22%20y2%3D%224.22%22%3E%3C%2Fline%3E%3C%2Fsvg%3E'%0A%0A%2F%2F%20Theme%20toggling%20logic%0Aconst%20toggleBtn%20%3D%20createElement('button'%2C%20%7B%0A%20%20innerHTML%3A%20sunSVG%2C%0A%20%20ariaLabel%3A%20'Switch%20theme'%2C%0A%20%20style%3A%20%60%0A%20%20%20%20position%3A%20fixed%3B%0A%20%20%20%20top%3A%2050px%3B%0A%20%20%20%20right%3A%2050px%3B%0A%20%20%20%20margin%3A%200%3B%0A%20%20%20%20padding%3A%2010px%3B%0A%20%20%20%20line-height%3A%201%3B%0A%20%20%60%0A%7D)%0A%0Alet%20theme%20%3D%20'light'%0Aconst%20toggleTheme%20%3D%20()%20%3D%3E%20%7B%0A%20%20if%20(theme%20%3D%3D%3D%20'light')%20%7B%0A%20%20%20%20theme%20%3D%20'dark'%0A%20%20%20%20toggleBtn.innerHTML%20%3D%20moonSVG%0A%20%20%20%20linkElm.href%20%3D%20'https%3A%2F%2Fcdn.jsdelivr.net%2Fnpm%2Fwater.css%402%2Fout%2Fdark.css'%0A%20%20%7D%20else%20%7B%0A%20%20%20%20theme%20%3D%20'light'%0A%20%20%20%20linkElm.href%20%3D%20'https%3A%2F%2Fcdn.jsdelivr.net%2Fnpm%2Fwater.css%402%2Fout%2Flight.css'%0A%20%20%20%20toggleBtn.innerHTML%20%3D%20sunSVG%0A%20%20%7D%0A%7D%0A%0AtoggleBtn.addEventListener('click'%2C%20toggleTheme)%0Adocument.body.append(toggleBtn)%7D)()%3B"> Test </a>

```js
javascript:(function()%7B%2F%2F%20Water.css%20Bookmarklet%0A%2F%2F%20---------------------%0A%0Aconst%20%24%24%20%3D%20(selector)%20%3D%3E%20document.querySelectorAll(selector)%0Aconst%20createElement%20%3D%20(tagName%2C%20properties)%20%3D%3E%20Object.assign(document.createElement(tagName)%2C%20properties)%0A%0A%2F%2F%20Remove%20all%20CSS%20stylesheets%2C%20external%20and%20internal%0A%24%24('link%5Brel%3D%22stylesheet%22%5D%2Cstyle').forEach((el)%20%3D%3E%20el.remove())%0A%0A%2F%2F%20Remove%20all%20inline%20styles%0A%24%24('*').forEach((el)%20%3D%3E%20(el.style%20%3D%20''))%0A%0Aconst%20linkElm%20%3D%20createElement('link'%2C%20%7B%0A%20%20rel%3A%20'stylesheet'%2C%0A%20%20href%3A%20'https%3A%2F%2Fcdn.jsdelivr.net%2Fnpm%2Fwater.css%402%2Fout%2Flight.css'%0A%7D)%0A%0A%2F%2F%20Add%20water.css%20and%20responsive%20viewport%20(if%20necessary)%0Adocument.head.append(%0A%20%20linkElm%2C%0A%20%20!%24%24('meta%5Bname%3D%22viewport%22%5D').length%20%26%26%20createElement('meta'%2C%20%7B%0A%20%20%20%20name%3A%20'viewport'%2C%0A%20%20%20%20content%3A%20'width%3Ddevice-width%2Cinitial-scale%3D1.0'%0A%20%20%7D)%0A)%0A%0A%2F%2F%20Theme%20switching%20icons%0Aconst%20moonSVG%20%3D%20'%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20width%3D%2224%22%20height%3D%2224%22%20viewBox%3D%220%200%2024%2024%22%20fill%3D%22none%22%20stroke%3D%22currentColor%22%20stroke-width%3D%222%22%20stroke-linecap%3D%22round%22%20stroke-linejoin%3D%22round%22%20class%3D%22feather%20feather-moon%22%3E%3Cpath%20d%3D%22M21%2012.79A9%209%200%201%201%2011.21%203%207%207%200%200%200%2021%2012.79z%22%3E%3C%2Fpath%3E%3C%2Fsvg%3E'%0Aconst%20sunSVG%20%3D%20'%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20width%3D%2224%22%20height%3D%2224%22%20viewBox%3D%220%200%2024%2024%22%20fill%3D%22none%22%20stroke%3D%22currentColor%22%20stroke-width%3D%222%22%20stroke-linecap%3D%22round%22%20stroke-linejoin%3D%22round%22%20class%3D%22feather%20feather-sun%22%3E%3Ccircle%20cx%3D%2212%22%20cy%3D%2212%22%20r%3D%225%22%3E%3C%2Fcircle%3E%3Cline%20x1%3D%2212%22%20y1%3D%221%22%20x2%3D%2212%22%20y2%3D%223%22%3E%3C%2Fline%3E%3Cline%20x1%3D%2212%22%20y1%3D%2221%22%20x2%3D%2212%22%20y2%3D%2223%22%3E%3C%2Fline%3E%3Cline%20x1%3D%224.22%22%20y1%3D%224.22%22%20x2%3D%225.64%22%20y2%3D%225.64%22%3E%3C%2Fline%3E%3Cline%20x1%3D%2218.36%22%20y1%3D%2218.36%22%20x2%3D%2219.78%22%20y2%3D%2219.78%22%3E%3C%2Fline%3E%3Cline%20x1%3D%221%22%20y1%3D%2212%22%20x2%3D%223%22%20y2%3D%2212%22%3E%3C%2Fline%3E%3Cline%20x1%3D%2221%22%20y1%3D%2212%22%20x2%3D%2223%22%20y2%3D%2212%22%3E%3C%2Fline%3E%3Cline%20x1%3D%224.22%22%20y1%3D%2219.78%22%20x2%3D%225.64%22%20y2%3D%2218.36%22%3E%3C%2Fline%3E%3Cline%20x1%3D%2218.36%22%20y1%3D%225.64%22%20x2%3D%2219.78%22%20y2%3D%224.22%22%3E%3C%2Fline%3E%3C%2Fsvg%3E'%0A%0A%2F%2F%20Theme%20toggling%20logic%0Aconst%20toggleBtn%20%3D%20createElement('button'%2C%20%7B%0A%20%20innerHTML%3A%20sunSVG%2C%0A%20%20ariaLabel%3A%20'Switch%20theme'%2C%0A%20%20style%3A%20%60%0A%20%20%20%20position%3A%20fixed%3B%0A%20%20%20%20top%3A%2050px%3B%0A%20%20%20%20right%3A%2050px%3B%0A%20%20%20%20margin%3A%200%3B%0A%20%20%20%20padding%3A%2010px%3B%0A%20%20%20%20line-height%3A%201%3B%0A%20%20%60%0A%7D)%0A%0Alet%20theme%20%3D%20'light'%0Aconst%20toggleTheme%20%3D%20()%20%3D%3E%20%7B%0A%20%20if%20(theme%20%3D%3D%3D%20'light')%20%7B%0A%20%20%20%20theme%20%3D%20'dark'%0A%20%20%20%20toggleBtn.innerHTML%20%3D%20moonSVG%0A%20%20%20%20linkElm.href%20%3D%20'https%3A%2F%2Fcdn.jsdelivr.net%2Fnpm%2Fwater.css%402%2Fout%2Fdark.css'%0A%20%20%7D%20else%20%7B%0A%20%20%20%20theme%20%3D%20'light'%0A%20%20%20%20linkElm.href%20%3D%20'https%3A%2F%2Fcdn.jsdelivr.net%2Fnpm%2Fwater.css%402%2Fout%2Flight.css'%0A%20%20%20%20toggleBtn.innerHTML%20%3D%20sunSVG%0A%20%20%7D%0A%7D%0A%0AtoggleBtn.addEventListener('click'%2C%20toggleTheme)%0Adocument.body.append(toggleBtn)%7D)()%3B
```

## WebDev Multi Tools

```js
javascript:(function () %7Bvar v %3D document.createElement(%27script%27)%3Bv.src %3D %27https%3A%2F%2Fcdn.jsdelivr.net%2Fgh%2FBrowncha023%2FVengeance%40v1.2.0%2Fscript.min.js%27%3Bdocument.body.appendChild(v)%3B%7D())
```

## Website Stack

```js
javascript:(function(){var el=document.createElement('script');el.type='text/javascript';el.src='https://micmro.github.io/performance-bookmarklet/dist/performanceBookmarklet.min.js';el.onerror=function(){alert("Looks like the Content Security Policy directive is blocking the use of bookmarklets\n\nYou can copy and paste the content of:\n\n\"https://micmro.github.io/performance-bookmarklet/dist/performanceBookmarklet.min.js\"\n\ninto your console instead\n\n(link is in console already)");console.log("https://micmro.github.io/performance-bookmarklet/dist/performanceBookmarklet.min.js");};document.getElementsByTagName('body')[0].appendChild(el);})();
```

## Website Dev Stack

```js
javascript: (function() { var d = document, e = d.getElementById('wappalyzer-container'); if (e !== null) { d.body.removeChild(e); } var u = 'https://www.wappalyzer.com/', t = new Date().getTime(), c = d.createElement('div'), p = d.createElement('div'), l = d.createElement('link'), s = d.createElement('script'); c.setAttribute('id', 'wappalyzer-container'); l.setAttribute('rel', 'stylesheet'); l.setAttribute('href', u + 'css/bookmarklet.css'); d.head.appendChild(l); p.setAttribute('id', 'wappalyzer-pending'); p.setAttribute('style', 'background-image: url(' + u + 'images/spinner.gif) !important'); c.appendChild(p); s.setAttribute('src', u + 'bookmarklet/wappalyzer.js'); s.onload = function() { window.wappalyzer = new Wappalyzer(); s = d.createElement('script'); s.setAttribute('src', u + 'bookmarklet/apps.js'); s.onload = function() { s = d.createElement('script'); s.setAttribute('src', u + 'bookmarklet/driver.js'); c.appendChild(s); }; c.appendChild(s); }; c.appendChild(s); d.body.appendChild(c); })();
```

## Website Stack - Built With

```js
javascript:void(open('https://builtwith.com/?'+encodeURIComponent(location.href)));
```