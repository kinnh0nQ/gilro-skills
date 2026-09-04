# 코드 레시피

`use_figma`로 실행하는 스니펫. 노드 ID는 가이드라인 파일(`RiSvBQQPpLexEs85NhomEW`) 기준이다.

## 폰트 먼저 로드

```js
for (const s of ['Bold','SemiBold','Regular']) {
  await figma.loadFontAsync({family:'Pretendard', style:s});
}
await figma.loadFontAsync({family:'ABeeZee', style:'Regular'});
```

## 커버 만들기 (가이드 클론)

```js
const src = await figma.getNodeByIdAsync('252:143');   // 2줄용. 1줄이면 252:150
const card = src.clone();
figma.currentPage.appendChild(card);
card.name = '카드_01_커버';
card.x = 0; card.y = 0;

// 텍스트 교체 — 폰트를 먼저 갈아끼운다
const texts = card.findAllWithCriteria({types:['TEXT']});
for (const t of texts) {
  const isHandle = t.characters.includes('@gilroro');
  t.fontName = isHandle
    ? {family:'ABeeZee', style:'Regular'}
    : {family:'Pretendard', style:'Bold'};
}
const sub  = texts.find(t => Math.round(t.fontSize) === 44);
const head = texts.find(t => Math.round(t.fontSize) === 80);
sub.characters  = '부제';
head.characters = '제목 첫 줄\n제목 둘째 줄';
return {cardId: card.id};
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

## 마지막장

```js
const src = await figma.getNodeByIdAsync('252:174');  // 까마귀 프로필 ver
const last = src.clone();
// 배경 이미지는 앞장에서 쓴 것을 재사용하고 오파시티만 조절
// 리드/푸터 문구는 고정값이므로 건드리지 않는다
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
