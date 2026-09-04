# 코드 레시피

`use_figma`로 실행하는 스니펫. 가이드 파일 `H1sxiDe60R9b28jykK4cew`의 템플릿을 복제하는 것이 기본이고,
아래 생성 코드는 같은 결과를 직접 만들 때 쓴다.

## 폰트 먼저 로드

```js
for (const s of ['Bold','SemiBold','Regular']) {
  await figma.loadFontAsync({family:'Pretendard', style:s});
}
await figma.loadFontAsync({family:'ABeeZee', style:'Regular'});
```

## 커버 만들기

```js
const G = 0.2549;                        // #414141
const card = figma.createFrame();
card.name = '카드_01_커버';
card.resize(1080, 1350);
card.clipsContent = true;
card.fills = [{type:'SOLID', color:{r:0,g:0,b:0}}];
figma.currentPage.appendChild(card);

// 배경 사진 (4:5에 가까울 때. 가로로 길면 아래 블러 레시피)
const photo = figma.createRectangle();
photo.resize(1080, 1350);
photo.fills = [{type:'IMAGE', scaleMode:'FILL', imageHash: HASH}];
card.appendChild(photo);

// 하단 블랙 오파시티
const grad = figma.createRectangle();
grad.resize(1080, 583); grad.y = 767;
grad.fills = [{
  type:'GRADIENT_LINEAR', blendMode:'MULTIPLY',
  gradientTransform: [[0,1,0],[-1,0,1]],
  gradientStops: [
    {color:{r:G,g:G,b:G,a:0},   position:0},
    {color:{r:G,g:G,b:G,a:0.8}, position:1}
  ]
}];
card.appendChild(grad);

// 제목 그리드 — 제목 1줄이면 y979, 2줄이면 y903
const grid = figma.createFrame();
grid.layoutMode = 'VERTICAL'; grid.itemSpacing = 10; grid.fills = [];
card.appendChild(grid);
grid.resize(849, 100);
grid.layoutSizingHorizontal = 'FIXED';
grid.layoutSizingVertical = 'HUG';
grid.x = 115.5; grid.y = 979;

function line(size, text){
  const t = figma.createText();
  t.fontName = {family:'Pretendard', style:'Bold'};
  t.fontSize = size; t.lineHeight = {unit:'AUTO'};
  t.characters = text;
  t.fills = [{type:'SOLID', color:{r:1,g:1,b:1}}];
  grid.appendChild(t);
  t.layoutSizingHorizontal = 'FILL'; t.textAutoResize = 'HEIGHT';
}
line(44, '부제');
line(80, '제목');

// 핸들
const handle = figma.createText();
handle.fontName = {family:'ABeeZee', style:'Regular'};
handle.fontSize = 28; handle.lineHeight = {unit:'PERCENT', value:170};
handle.characters = '@gilroro.mag';
handle.fills = [{type:'SOLID', color:{r:1,g:1,b:1}}];
card.appendChild(handle);
handle.x = 448; handle.y = 1232;
```

## 피드 만들기

```js
const card = figma.createFrame();
card.resize(1080, 1350); card.clipsContent = true;
card.fills = [{type:'SOLID', color:{r:0,g:0,b:0}}];
figma.currentPage.appendChild(card);

const photo = figma.createRectangle();
photo.resize(1080, 1350);
photo.fills = [{type:'IMAGE', scaleMode:'FILL', imageHash: HASH}];
card.appendChild(photo);

const bub = figma.createFrame();
bub.name = '말풍선_오토레이아웃';
bub.layoutMode = 'VERTICAL'; bub.itemSpacing = 10;
bub.paddingTop = 20; bub.paddingBottom = 20;
bub.paddingLeft = 30; bub.paddingRight = 30;
bub.cornerRadius = 16;
bub.fills = [{type:'SOLID', color:{r:1,g:1,b:1}}];
bub.strokes = [{type:'SOLID', color:{r:0,g:0,b:0}}];
bub.strokeWeight = 2;
card.appendChild(bub);
bub.primaryAxisSizingMode = 'AUTO';
bub.counterAxisSizingMode = 'AUTO';

const t = figma.createText();
t.fontName = {family:'Pretendard', style:'SemiBold'};
t.fontSize = 36; t.lineHeight = {unit:'PERCENT', value:170};
t.fills = [{type:'SOLID', color:{r:0,g:0,b:0}}];
t.characters = ['가게 이름','주소 · 가까운 역','대표메뉴'].join('\n');
bub.appendChild(t);
bub.x = 85; bub.y = 1074;      // 고정 — 폭·높이만 가변
```

## 피드 말풍선 문구 교체

```js
const card = await figma.getNodeByIdAsync(CARD_ID);
const bubble = card.findOne(n => n.name.includes('말풍선'));
const t = bubble.findAllWithCriteria({types:['TEXT']})[0];
await figma.loadFontAsync({family:'Pretendard', style:'SemiBold'});
t.fontName = {family:'Pretendard', style:'SemiBold'};
t.fontSize = 36;
t.lineHeight = {unit:'PERCENT', value:170};
t.characters = '본문 한 문장. 최대 3줄.';
// 말풍선은 HUG — x, y는 건드리지 않는다 (x85 y1074 고정)
```

## 가로로 긴 이미지 (16:9 등) 처리

```js
// 1) 배경: 같은 이미지를 1360×1630으로 깔고 블러 + 어둡게
const bg = figma.createRectangle();
bg.resize(1360, 1630);
bg.x = (1080 - 1360) / 2;
bg.y = (1350 - 1630) / 2;
bg.fills = [{type:'IMAGE', scaleMode:'FILL', imageHash: HASH}];
bg.effects = [{type:'LAYER_BLUR', radius:64, visible:true}];

const shade = figma.createRectangle();
shade.resize(1080, 1350);
shade.fills = [{type:'SOLID', color:{r:0,g:0,b:0}, opacity:0.55}];

// 2) 그 위에 원본 비율 밴드
const band = figma.createRectangle();
band.resize(1080, 608);
band.y = 371;
band.fills = [{type:'IMAGE', scaleMode:'FILL', imageHash: HASH}];
```

## 가이드 템플릿 복제 (같은 파일에서 작업할 때)

```js
const T = { cover2:'1:9', cover1:'1:16', feed:'1:24', ending:'1:40' };
const src = await figma.getNodeByIdAsync(T.feed);
const card = src.clone();
figma.currentPage.appendChild(card);
card.x = X; card.y = Y;
// 텍스트는 폰트를 먼저 갈아끼운 뒤 대입 (아래 폰트 함정 참고)
```

## 마지막장 (엔딩)

프로필이 이미지 에셋이라 **`1:40`(까마귀 ver)을 복제**한다.

```js
const last = (await figma.getNodeByIdAsync('1:40')).clone();
figma.currentPage.appendChild(last);
last.x = X; last.y = Y;

// 배경은 반드시 이번 회차 앞장 이미지로 교체하고 오파시티만 조절한다.
const bg = last.findOne(n => n.fills && n.fills[0] && n.fills[0].type === 'IMAGE');
if (bg) bg.fills = [{type:'IMAGE', scaleMode:'FILL', imageHash: 앞장_HASH}];
// 리드/프로필/푸터 문구는 고정이므로 건드리지 않는다.
```

## 새 사진 올리기

파일에 없는 사진은 `upload_assets`로 넣는다 (`createImageAsync`는 쓰지 않는다).

```js
// 1) 사진용 사각형을 먼저 만들고 ID를 모은다
const targets = cards.map(function(card){
  const r = figma.createRectangle();
  r.resize(1080, 1350);
  card.insertChild(0, r);
  return r.id;
});
return {targets};
```

```
// 2) upload_assets(fileKey, count: N, nodeIds: targets, scaleMode: 'FILL')
// 3) 반환된 URL마다 순서대로 POST
curl -X POST --data-binary @photo1.jpg -H 'Content-Type: image/jpeg' '<URL 1>'
```

## 검증

```js
const cards = figma.currentPage.children.filter(n => n.name.startsWith('카드_'));
return cards.map(c => {
  const bubble = c.findOne(n => n.name.includes('말풍선'));
  const t = bubble && bubble.findAllWithCriteria({types:['TEXT']})[0];
  return {
    name: c.name,
    size: Math.round(c.width) + '×' + Math.round(c.height),
    bubbleXY: bubble ? Math.round(bubble.x) + ',' + Math.round(bubble.y) : '-',
    lines: t ? t.characters.split('\n').length : 0,
    font: t ? t.fontName.family + ' ' + t.fontName.style : '-'
  };
});
```
