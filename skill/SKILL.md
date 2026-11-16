---
name: "histoire-argent-animee"
description: "Crée les vidéos TikTok animées d'histoires sur l'argent (couple, salaire, épargne, famille, business) : script viral, voix off, montage bleu nuit/jaune avec personnages illustrés grands et animés."
---

# Histoires d'argent animées (compte TikTok XANA FAX IA/DIGITAL)

Objectif du compte : devenir viral et gagner des abonnés (objectif 10K). Format inspiré d'Idéo Stories : une histoire vraie, des personnages, des calculs en FCFA, un débat en commentaire.

Utilise ce skill dès que l'utilisateur demande une vidéo, un script, une idée ou le contenu du jour pour ce compte, ou une histoire sur l'argent, le couple, le salaire, l'épargne, la famille ou le business.

## 1. Le script

**Sujets qui font le plus de vues** (ceux qui font débattre) :
- couple et argent (moitié-moitié, dot, qui paie quoi) ;
- famille et argent (le grand frère qui paie tout, les demandes du village) ;
- les salaires réels (« 75 000 F par mois, comment il fait ? ») ;
- les questions « Et toi ? » (« avec 1 million, tu fais quoi ? »).

Le digital et l'IA peuvent être la solution dans certaines histoires, mais la vidéo ne doit pas ressembler à une publicité.

**Structure :**
1. **Accroche** : un chiffre et une situation en une phrase. Par exemple : « Il gagne 300 000 F, elle 100 000. Moitié-moitié ? »
2. **Les personnages** : un nom, un métier, un salaire précis.
3. **Le problème vécu**, avec des dialogues entre guillemets.
4. **Les calculs**, ligne par ligne et justes. Toujours vérifier l'arithmétique.
5. **Le tournant** : un conseil, une rencontre ou une décision.
6. **Le nouveau calcul ou le résultat.**
7. **Un « Attention »** qui nuance, pour la crédibilité.
8. **La morale** en une phrase.
9. **Une question à deux choix** (« moitié-moitié… ou selon le salaire ? »), « Dis-le en commentaire », puis une demande de partage ciblée.

**Règles :**
- Durée de 1 min 30 à 2 min pour une histoire. Si l'utilisateur demande un format court, 30 à 60 s.
- Jamais d'annonce de suite (pas de « la suite dans l'épisode 2 »).
- **Changer les noms à chaque vidéo** pour que les mêmes ne reviennent pas. Noms sénégalais variés : Malick, Coumba, Ndèye, Ablaye, Fatou, Cheikh, Khady, Modou, Astou, Pape, Mariama, Ousseynou, Bineta, Saliou…
- Les montants sont écrits en chiffres pour la voix (« 120 000 », « 75 % »).

Livrer le script, puis faire la vidéo seulement si l'utilisateur la demande.

## 2. La voix off

Utiliser edge-tts avec la voix fr-FR-HenriNeural, la vitesse +15 % et `boundary="WordBoundary"` pour obtenir le temps de chaque mot. Faire une scène par phrase-bloc, avec 0,3 s d'écart entre les scènes. Ajouter 1,6 s au total pour l'outro.

```python
import edge_tts, ssl
import edge_tts.communicate as _ec
_ec._SSL_CTX = ssl.create_default_context(cafile="/root/.ccr/ca-bundle.crt")
c = edge_tts.Communicate(text, "fr-FR-HenriNeural", rate="+15%", boundary="WordBoundary")
# Sur le stream : chunk audio -> fichier mp3 ; WordBoundary -> {w, s=offset/1e7, e=(offset+duration)/1e7}
# Enregistrer timing.json : {total, scenes:[{id, ch(étiquette de chapitre), text, start, end, words}]}
# Assembler les mp3 avec ffmpeg adelay + amix -> voix.wav
```

## 3. Le son : PAS d'effets sonores

L'utilisateur n'aime pas les effets sonores (whoosh, boom, bips, clics, arpèges) : ils sont toujours les mêmes d'une vidéo à l'autre. **On n'en met aucun.** La piste audio finale est la voix seule, normalisée :

```
ffmpeg -i voix.wav -af loudnorm=I=-14:TP=-1.5:LRA=11 -ar 44100 mix.wav
```

Si l'utilisateur demande une musique de fond, il fournit le fichier ou on en reparle. Ne jamais remettre les effets synthétiques.

## 4. Le style visuel

- **Format** : vertical 1080×1920, 30 i/s.
- **Fond** : dégradé bleu nuit (#0F2557 vers un bleu plus clair) avec une grille légère et un cadre doré fin.
- **Couleurs** : jaune GOLD #FFC93C, blanc, vert #1FA463 pour le positif, rouge #FF5A5F pour le négatif. Pas de beige, pas de noir et blanc.
- **Polices** : Anton (titres), Bebas Neue (étiquettes et HUD), Poppins 600, 800 et 900 (texte), Noto Color Emoji. On les obtient avec `npm pack @fontsource/...`.
- **Sous-titres karaoké en haut** (vers y=520) : Poppins 800 52 px, mot par mot. Les mots clés et les chiffres sont surlignés en jaune avec le texte en bleu foncé.
- **Étiquette de chapitre** : une pastille blanche vers y=665.
- **HUD en haut à gauche** : un compteur qui s'anime (montants, dettes, heures, %).
- **En haut à droite** : « XANA FAX / DIGITAL ».
- **Barre de progression** jaune tout en haut.
- **Outro** : fond bleu, « XANA FAX », une barre jaune, « DIGITAL », puis « Créer. Apprendre. Avancer. ».

**Rendu** : un Canvas 2D avec une fonction déterministe `renderFrame(t)`. Playwright appelle `frame(t)`, récupère `canvas.toDataURL('image/jpeg')` et l'envoie en mjpeg dans ffmpeg (libx264 crf 19, yuv420p, AAC 192k, +faststart, `-shortest`, avec mix.wav). Avant le rendu complet, toujours faire une planche d'images de contrôle et la regarder (chevauchements, textes coupés, éléments trop petits).

## 5. Les règles d'animation (les plus importantes)

### a) Personnages et objets GRANDS

- Un personnage seul se dessine avec une échelle k = 0,95 à 1,15. Il mesure environ 600 à 720 px de haut, soit plus d'un tiers de l'écran.
- Deux personnages : k = 0,85 à 1,0, placés vers x = 270 et x = 810.
- Ne jamais descendre sous k = 0,7, sauf pour un personnage secondaire au fond.
- Le sol est vers y = 1700 à 1750, pour laisser de la place aux grands personnages.
- Les objets (téléphone, maison, billets, voiture, calendrier, cartes de calcul) font au moins 300 px. L'objet principal de la scène peut prendre toute la largeur.
- Pour faire de la place, on réduit les textes, pas les personnages.

### b) Chaque chose citée est représentée

Dès que la voix nomme un objet, un lieu, une personne ou un montant, son dessin apparaît **au moment exact du mot**, grâce à `wf(scene, 'mot')`. Par exemple :
- voiture → une voiture qui entre en roulant ;
- loyer → une maison ;
- salaire → des billets ou une carte avec le montant ;
- message → un téléphone avec une bulle WhatsApp ;
- marché → des étals ;
- sœur → un nouveau personnage qui entre.

Avant de coder, faire la liste de tous les noms concrets du script et prévoir un dessin pour chacun. Construire une petite bibliothèque d'objets en Canvas, réutilisable : maison, boutique, téléphone, voiture, moto, billet, pièces, calendrier, horloge, sac, congélateur, bouteille, ordinateur, cahier.

### c) Entrées et sorties gauche-droite

Rien n'apparaît sur place sans mouvement.
- **Un personnage** entre en marchant, du bord gauche ou droit (avec `walk:true`, qui fait bouger les jambes et les bras), s'arrête à sa place, puis ressort par l'autre côté ou par le même côté en fin de scène.
- **Un objet** glisse depuis le côté avec un léger dépassement (easing back), puis repart sur le côté à la fin de sa vie.
- **Entre deux scènes**, le décor sortant part vers la gauche pendant que le nouveau entre par la droite (environ 0,4 s), ce qui donne une impression de caméra qui se déplace.
- **Pendant qu'il parle**, un personnage n'est jamais figé : il change de pose, change d'expression et respire.

```js
// Entrée/sortie horizontale : renvoie le décalage x à ajouter
function slideX(t,tIn,tOut,dir=1,dist=900,dur=.45){ // dir=1 : vient de la droite, -1 : de la gauche
 const a=eo(pr(t,tIn,tIn+dur)), b=pr(t,tOut-dur,tOut);
 return dir*dist*(1-a) - dir*dist*(b*b);
}
// Exemple : une personne entre à gauche en marchant, puis sort à droite
// const dx=slideX(t,t0,SC[id].end,-1); MAL(300+dx,1720,1.0,seq,t,'happy',{walk:Math.abs(dx)>2});
// Exemple : un objet
// X.save();X.translate(slideX(t,wf(id,'voiture'),SC[id].end,1),0);car(540,1400,1.2);X.restore();
```

**Poses enchaînées** : `PA([[t0,'stand'],[wf(id,'mot'),'point']],t)` interpole entre les poses en 0,3 s.

**Poses disponibles** : stand, wave, up, point, think, worried, phone, carry, curl, squat, shrug, hips.

**Options** : walk, phone, handBag, shoulderBag, paper, dumbbell, anim:'curl', flip, logo.

**Visages** : neutral, happy, laugh, worried, sad, surprised, angry.

### d) Les femmes ont une silhouette de femme bien visible

Option `female:true` : robe pagne longue avec motifs, taille marquée et ceinture, épaules plus fines, jambes visibles sous la robe, foulard (`hair:'wrap'`), boucles d'oreilles dorées, cils et rouge à lèvres.

## 6. Code des personnages (les personnages propres à l'utilisateur, à réutiliser tel quel)

Il faut d'abord définir `X` (le contexte 2D), `lerp`, `rr` (roundRect), `txt`, `BLK='#0F2557'` et `GOLD='#FFC93C'`.

```js
function bust(x,y,s,o={}){const t=o.t||0;X.save();X.translate(x,y+Math.sin(t*2)*2);X.scale(s,s);
 const L=BLK,lw=6,skin=o.skin||'#7A4B2A',sh=o.shirt||'#8FB3E8';
 X.lineJoin='round';X.lineCap='round';
 if(!o.noBody){
 X.beginPath();X.moveTo(-150,200);X.quadraticCurveTo(-150,70,-60,50);X.lineTo(60,50);X.quadraticCurveTo(150,70,150,200);X.closePath();X.fillStyle=sh;X.fill();X.strokeStyle=L;X.lineWidth=lw;X.stroke();
 X.fillStyle='rgba(0,0,0,.12)';X.beginPath();X.moveTo(-150,200);X.quadraticCurveTo(-150,120,-110,85);X.lineTo(-100,200);X.closePath();X.fill();
 X.fillStyle=skin;X.fillRect(-26,18,52,44);X.strokeRect(-26,18,52,44);
 if(o.collar!==false){X.fillStyle=o.collar||'#fff';X.beginPath();X.moveTo(-48,50);X.lineTo(0,92);X.lineTo(48,50);X.lineTo(28,46);X.lineTo(0,70);X.lineTo(-28,46);X.closePath();X.fill();X.stroke()}
 if(o.tie){X.fillStyle=o.tie;X.beginPath();X.moveTo(-10,70);X.lineTo(10,70);X.lineTo(16,150);X.lineTo(0,168);X.lineTo(-16,150);X.closePath();X.fill();X.stroke()}
 }
 X.fillStyle=skin;for(const ex of[-74,74]){X.beginPath();X.ellipse(ex,-40,14,20,0,0,7);X.fill();X.stroke()}
 X.beginPath();X.ellipse(0,-50,74,84,0,0,7);X.fillStyle=skin;X.fill();X.stroke();
 X.fillStyle='rgba(255,255,255,.10)';X.beginPath();X.ellipse(-26,-84,26,18,-.5,0,7);X.fill();
 if(o.beard){X.fillStyle=o.beard;X.beginPath();X.moveTo(-62,-20);X.quadraticCurveTo(-60,40,0,40);X.quadraticCurveTo(60,40,62,-20);X.quadraticCurveTo(40,10,0,6);X.quadraticCurveTo(-40,10,-62,-20);X.fill();X.lineWidth=4;X.stroke();X.lineWidth=lw}
 const hc=o.hat||'#1a1a1a';
 if(o.hair==='cap'){X.fillStyle=hc;X.beginPath();X.ellipse(0,-108,76,40,0,Math.PI,0);X.fill();X.stroke();X.beginPath();X.ellipse(48,-104,58,14,.08,0,7);X.fill();X.stroke();X.fillStyle='rgba(255,255,255,.18)';X.beginPath();X.ellipse(-20,-126,26,10,-.2,0,7);X.fill()}
 else if(o.hair==='kufi'){X.fillStyle=hc;X.beginPath();X.moveTo(-64,-100);X.quadraticCurveTo(-60,-150,0,-152);X.quadraticCurveTo(60,-150,64,-100);X.closePath();X.fill();X.stroke();X.strokeStyle='rgba(0,0,0,.25)';X.lineWidth=3;for(let i=-3;i<=3;i++){X.beginPath();X.moveTo(i*16,-148);X.lineTo(i*18,-104);X.stroke()}X.strokeStyle=L;X.lineWidth=lw}
 else if(o.hair==='wrap'){X.fillStyle=hc;X.beginPath();X.moveTo(-80,-70);X.bezierCurveTo(-90,-170,40,-190,80,-100);X.quadraticCurveTo(0,-120,-80,-70);X.closePath();X.fill();X.stroke();X.beginPath();X.ellipse(56,-150,30,22,-.6,0,7);X.fill();X.stroke()}
 else {X.fillStyle='#141414';X.beginPath();X.moveTo(-72,-62);X.quadraticCurveTo(-80,-148,0,-140);X.quadraticCurveTo(80,-148,72,-62);X.quadraticCurveTo(60,-110,0,-112);X.quadraticCurveTo(-60,-110,-72,-62);X.fill()}
 if(o.grey){X.fillStyle='#d8d8d8';X.beginPath();X.ellipse(-66,-70,10,22,0,0,7);X.ellipse(66,-70,10,22,0,0,7);X.fill()}
 const f=o.face||'neutral';const bl=(t%3.3)<.12;
 X.strokeStyle='#141414';X.lineWidth=7;X.beginPath();
 if(f==='worried'||f==='sad'){X.moveTo(-46,-82);X.lineTo(-18,-90);X.moveTo(46,-82);X.lineTo(18,-90)}
 else if(f==='angry'){X.moveTo(-46,-94);X.lineTo(-18,-84);X.moveTo(46,-94);X.lineTo(18,-84)}
 else if(f==='surprised'){X.moveTo(-46,-98);X.quadraticCurveTo(-32,-106,-18,-98);X.moveTo(46,-98);X.quadraticCurveTo(32,-106,18,-98)}
 else {X.moveTo(-46,-88);X.quadraticCurveTo(-32,-94,-18,-88);X.moveTo(46,-88);X.quadraticCurveTo(32,-94,18,-88)}
 X.stroke();
 for(const ex of[-30,30]){if(bl){X.lineWidth=5;X.beginPath();X.moveTo(ex-12,-62);X.lineTo(ex+12,-62);X.stroke()}else{X.fillStyle='#fff';X.beginPath();X.ellipse(ex,-62,14,f==='surprised'?17:13,0,0,7);X.fill();X.lineWidth=3;X.stroke();X.fillStyle='#141414';X.beginPath();X.arc(ex+3,-60,7,0,7);X.fill();X.fillStyle='#fff';X.beginPath();X.arc(ex+5,-63,2.4,0,7);X.fill()}}
 if(o.glasses){X.strokeStyle='#141414';X.lineWidth=4;for(const ex of[-30,30]){X.beginPath();X.arc(ex,-62,22,0,7);X.stroke()}X.beginPath();X.moveTo(-8,-62);X.lineTo(8,-62);X.stroke()}
 if(o.female&&!bl){X.strokeStyle='#141414';X.lineWidth=4;for(const ex of[-30,30]){const d=ex<0?-1:1;X.beginPath();X.moveTo(ex+d*12,-70);X.lineTo(ex+d*22,-80);X.moveTo(ex+d*6,-74);X.lineTo(ex+d*12,-86);X.stroke()}}
 X.strokeStyle='rgba(0,0,0,.45)';X.lineWidth=4;X.beginPath();X.moveTo(-8,-30);X.quadraticCurveTo(0,-22,8,-30);X.stroke();
 X.strokeStyle='#3a1a0e';X.lineWidth=5;X.beginPath();
 if(f==='happy'){X.moveTo(-26,-6);X.quadraticCurveTo(0,20,26,-6);X.stroke()}
 else if(f==='laugh'){X.fillStyle='#3a1a0e';X.moveTo(-30,-10);X.quadraticCurveTo(0,34,30,-10);X.closePath();X.fill();X.fillStyle='#fff';X.beginPath();X.ellipse(0,-6,20,5,0,0,Math.PI);X.fill()}
 else if(f==='worried'||f==='sad'){X.moveTo(-22,6);X.quadraticCurveTo(0,-8,22,6);X.stroke()}
 else if(f==='surprised'){X.fillStyle='#3a1a0e';X.beginPath();X.ellipse(0,0,10,14,0,0,7);X.fill()}
 else if(f==='angry'){X.moveTo(-20,0);X.lineTo(20,0);X.stroke()}
 else {X.moveTo(-18,-2);X.quadraticCurveTo(0,6,18,-2);X.stroke()}
 if(o.female&&(f==='neutral'||f==='happy'||f==='sad'||f==='worried')){X.fillStyle=o.lips||'#B0304A';X.beginPath();const up=(f==='happy')?1:(f==='neutral'?0:-1);X.ellipse(0,(f==='happy'?2:0),20,7+up*2,0,0,7);X.fill()}
 X.fillStyle='rgba(255,120,100,.18)';X.beginPath();X.arc(-48,-30,12,0,7);X.arc(48,-30,12,0,7);X.fill();
 if(o.female){X.strokeStyle=GOLD;X.lineWidth=5;for(const ex of[-74,74]){X.beginPath();X.arc(ex,-4,11,0,7);X.stroke()}}
 X.restore()}

const R=Math.PI/180;
function seg(x,y,a,l){return[x+Math.sin(a*R)*l,y+Math.cos(a*R)*l]}
const POSE={
 stand:{aL:[8,-4],aR:[8,-4],lL:[3,0],lR:[3,0]}, wave:{aL:[8,-4],aR:[140,30],lL:[3,0],lR:[3,0]},
 up:{aL:[150,10],aR:[150,10],lL:[6,0],lR:[6,0]}, point:{aL:[8,-4],aR:[95,0],lL:[3,0],lR:[3,0]},
 think:{aL:[-20,-100],aR:[-12,-150],lL:[3,0],lR:[3,0]}, worried:{aL:[150,-115],aR:[150,-115],lL:[3,0],lR:[3,0]},
 phone:{aL:[8,-4],aR:[-20,-145],lL:[3,0],lR:[3,0]}, carry:{aL:[-25,-95],aR:[10,0],lL:[3,0],lR:[3,0]},
 curl:{aL:[10,-100],aR:[10,-100],lL:[8,0],lR:[8,0]}, squat:{aL:[70,-120],aR:[70,-120],lL:[45,-85],lR:[45,-85]},
 shrug:{aL:[40,85],aR:[40,85],lL:[3,0],lR:[3,0]}, hips:{aL:[40,-110],aR:[40,-110],lL:[3,0],lR:[3,0]},
};
function lerpPose(a,b,p){const o={};for(const k in a)o[k]=a[k].map((v,i)=>lerp(v,b[k][i],p));return o}
function PA(seq,t){let a=seq[0][1],b=a,p=1;for(let i=0;i<seq.length;i++){if(t>=seq[i][0]){a=i>0?seq[i-1][1]:seq[i][1];b=seq[i][1];p=eo(pr(t,seq[i][0],seq[i][0]+.3))}}return lerpPose(POSE[a],POSE[b],p)}
function person(x,y,k,pose,o={}){const t=o.t||0;const P=typeof pose==='string'?POSE[pose]:pose;
 X.save();X.translate(x,y);X.scale(k*(o.flip?-1:1),k);X.lineCap='round';X.lineJoin='round';
 const L=BLK,skin=o.skin||'#6E4325',sh=o.shirt||'#FFC93C',pants=o.pants||'#25324F',shoe=o.shoe||'#111';
 const squat=P.lL[1]<-30||P.lR[1]>30;const hipY=squat?-170:-235+(o.walk?Math.abs(Math.sin(t*6))*-6:0);
 X.fillStyle='rgba(0,0,0,.25)';X.beginPath();X.ellipse(0,4,90,14,0,0,7);X.fill();
 let lL=P.lL,lR=P.lR;if(o.walk){const s=Math.sin(t*6)*16;lL=[lL[0]+s,lL[1]];lR=[lR[0]-s,lR[1]]}
 for(const [hx,leg0] of[[-26,lL],[26,lR]]){const sg=hx<0?-1:1;const leg=[leg0[0]*sg,leg0[1]*sg];const k1=seg(hx,hipY,leg[0],118),f=seg(k1[0],k1[1],leg[0]+leg[1],118);
  X.strokeStyle=L;X.lineWidth=o.female?38:50;X.beginPath();X.moveTo(hx*(o.female?.7:1),hipY);X.lineTo(...k1);X.lineTo(...f);X.stroke();X.strokeStyle=o.female?skin:pants;X.lineWidth=o.female?28:40;X.stroke();
  X.fillStyle=shoe;X.beginPath();X.ellipse(f[0]+(hx<0?-10:10),f[1]+4,32,15,0,0,7);X.fill()}
 const sy=hipY-200;
 if(o.female){const dh=o.dressLen||175;const pat=o.pattern||'rgba(255,255,255,.35)';
  const dp=()=>{X.beginPath();X.moveTo(-64,sy+28);X.quadraticCurveTo(-66,sy,-36,sy);X.lineTo(36,sy);X.quadraticCurveTo(66,sy,64,sy+28);X.quadraticCurveTo(58,sy+80,44,sy+120);X.quadraticCurveTo(78,hipY,96,hipY+dh);X.lineTo(-96,hipY+dh);X.quadraticCurveTo(-78,hipY,-44,sy+120);X.quadraticCurveTo(-58,sy+80,-64,sy+28);X.closePath()};dp();
  X.fillStyle=sh;X.fill();X.save();X.clip();X.fillStyle=pat;for(let yy=sy+10;yy<hipY+dh;yy+=34)for(let xx=-100;xx<110;xx+=34){X.beginPath();X.arc(xx+((yy/34)%2)*17,yy,6,0,7);X.fill()}X.restore();
  dp();X.strokeStyle=L;X.lineWidth=6;X.stroke();
  X.fillStyle=o.belt||GOLD;X.fillRect(-46,sy+112,92,16);X.strokeRect(-46,sy+112,92,16);
  X.fillStyle=skin;X.beginPath();X.moveTo(-26,sy);X.quadraticCurveTo(0,sy+34,26,sy);X.fill();X.strokeStyle=L;X.lineWidth=5;X.stroke()}
 else{X.beginPath();X.moveTo(-58,hipY+10);X.lineTo(-74,sy+30);X.quadraticCurveTo(-74,sy,-40,sy);X.lineTo(40,sy);X.quadraticCurveTo(74,sy,74,sy+30);X.lineTo(58,hipY+10);X.closePath();X.fillStyle=sh;X.fill();X.strokeStyle=L;X.lineWidth=6;X.stroke();
  X.fillStyle='rgba(0,0,0,.12)';X.fillRect(-58,hipY-12,116,22)}
 if(o.logo){txt(o.logo,0,sy+90,{font:'Anton',size:40,color:o.logoCol||BLK})}
 if(o.shoulderBag){X.strokeStyle='#5C3B2A';X.lineWidth=10;X.beginPath();X.moveTo(-50,sy+6);X.lineTo(60,hipY-10);X.stroke();X.fillStyle=o.shoulderBag;rr(30,hipY-40,90,70,14);X.fill();X.strokeStyle=L;X.lineWidth=5;X.stroke()}
 const hands=[];const SX=o.female?56:66;
 for(const [sx,arm] of[[-SX,P.aL],[SX,P.aR]]){let a=arm.slice();if(o.walk){a[0]+=Math.sin(t*6+(sx<0?Math.PI:0))*14}
  if(o.anim==='curl'){const c=(Math.sin(t*4)+1)/2;a=[10,lerp(-20,-145,c)]}
  const sg=sx<0?-1:1;a=[a[0]*sg,a[1]*sg];
  const e=seg(sx,sy+18,a[0],92),h=seg(e[0],e[1],a[0]+a[1],86);hands.push(h);
  X.strokeStyle=L;X.lineWidth=40;X.beginPath();X.moveTo(sx,sy+18);X.lineTo(...e);X.lineTo(...h);X.stroke();
  X.strokeStyle=skin;X.lineWidth=28;X.beginPath();X.moveTo(...e);X.lineTo(...h);X.stroke();
  X.strokeStyle=sh;X.lineWidth=32;X.beginPath();X.moveTo(sx,sy+18);X.lineTo(lerp(sx,e[0],.9),lerp(sy+18,e[1],.9));X.stroke();
  X.fillStyle=skin;X.beginPath();X.arc(h[0],h[1],19,0,7);X.fill();X.strokeStyle=L;X.lineWidth=5;X.stroke();
  X.beginPath();X.ellipse(h[0]+(sx<0?11:-11),h[1]-8,7,11,sx<0?.6:-.6,0,7);X.fill();X.stroke()}
 const [hL,hR]=hands;
 if(o.handBag){X.strokeStyle='#5C3B2A';X.lineWidth=7;X.beginPath();X.arc(hR[0],hR[1]+16,22,Math.PI,0);X.stroke();X.fillStyle=o.handBag;rr(hR[0]-50,hR[1]+16,100,80,16);X.fill();X.strokeStyle=L;X.lineWidth=5;X.stroke();X.fillStyle=GOLD;rr(hR[0]-12,hR[1]+28,24,14,4);X.fill()}
 if(o.phone){X.fillStyle='#111';rr(hR[0]-16,hR[1]-58,32,58,7);X.fill();X.fillStyle='#8FE3C6';rr(hR[0]-12,hR[1]-54,24,46,4);X.fill()}
 if(o.dumbbell){for(const h of hands){X.fillStyle='#333';X.fillRect(h[0]-34,h[1]-5,68,10);X.fillStyle='#111';rr(h[0]-44,h[1]-20,16,40,4);X.fill();rr(h[0]+28,h[1]-20,16,40,4);X.fill()}}
 if(o.paper){X.fillStyle='#fff';X.save();X.translate(hR[0],hR[1]);X.rotate(-.2);rr(-30,-70,60,80,4);X.fill();X.strokeStyle=L;X.lineWidth=3;X.stroke();X.restore()}
 X.fillStyle=skin;X.fillRect(-24,sy-34,48,40);X.strokeStyle=L;X.lineWidth=6;X.strokeRect(-24,sy-34,48,40);
 X.restore();
 bust(x,y+(sy-62)*k,k*.98,{...o,noBody:true,flip:o.flip});
}
```

**Préréglages de personnages** (changer les noms et les couleurs à chaque vidéo) :

```js
const CH={
 homme_bleu:{skin:'#5E3B22',shirt:'#2F6FD6',pants:'#1c2540',hair:'short',collar:'#fff'},
 homme_casquette:{skin:'#6E4325',shirt:'#F29A8E',hair:'cap',hat:'#E8322F',collar:'#fff'},
 homme_jaune:{skin:'#5A3720',shirt:'#FFC93C',hair:'short',collar:'#fff'},
 vieux_sage:{skin:'#4E3120',shirt:'#E9E4D8',pants:'#E9E4D8',hair:'kufi',hat:'#7A1F2B',beard:'#e0e0e0',grey:true,collar:false},
 femme_orange:{skin:'#7A4B2A',shirt:'#E8742F',hair:'wrap',hat:'#1FA463',female:true,collar:false,pattern:'rgba(255,214,90,.55)',belt:'#1FA463'},
 femme_violette:{skin:'#6B4024',shirt:'#B03A8C',hair:'wrap',hat:'#FFC93C',female:true,collar:false,pattern:'rgba(255,255,255,.4)'},
};
```

## 7. Pièges connus

- **Correspondance des mots** : normaliser les apostrophes et les ligatures (« l'homme » devient « lhomme », « cœur » devient « cur », « moitié-moitié » devient « moitiemoitie »). Fusionner les morceaux vides comme « % » avec le mot précédent dans les sous-titres. Afficher un `console.log('MISS', ...)` pour repérer les mots non trouvés.
- **Sous-titres** : calculer les débuts et fins de toutes les phrases avant de calculer leur durée d'affichage.
- **Compteurs** : le compteur du HUD et les barres du graphique doivent utiliser la même progression, arrondie au millier.
- **Chevauchements** : vérifier sur les images de contrôle que les titres et objets ne passent ni sur l'étiquette de chapitre (y≈665) ni sur les sous-titres (y≈450–600).

## 8. Ce qu'on livre

- La vidéo MP4 et la voix off MP3, envoyées dans la conversation.
- La légende TikTok avec 4 à 6 hashtags : 1 gros hashtag, le sujet précis, 1 ou 2 de niche, et #pourtoi.
- Pour YouTube Shorts : un titre et des tags.
- Une idée de suite, à proposer seulement si la vidéo marche, sans l'annoncer dans la vidéo.