# lumei-lamp MCP

LUMEI 가상 조명을 Claude ↔ MCP ↔ 브라우저 방식으로 원격 조작하는 프로젝트입니다.

## 구조

```
Claude (claude.ai)
   │  MCP tool 호출
   ▼
Cloudflare Workers  (/mcp)
   │  상태를 KV에 저장
   ▼
Cloudflare KV  ("state" 키)
   ▲  2초마다 폴링
   │
브라우저 (virtual.html)
```

## 제공 MCP Tools

| Tool | 설명 |
|---|---|
| `get_product_info` | 제품 정보 조회 |
| `get_lamp_state` | 현재 상태 조회 (전원·밝기·모드·배터리·추적 여부) |
| `get_charging_state` | 배터리 잔량 조회 |
| `set_power` | 전원 ON/OFF |
| `set_brightness` | 밝기 절댓값 설정 (0~100) |
| `set_mode` | 조명 모드 변경 (focus / relax / night) |
| `set_tracking` | 물체 추적 ON/OFF |
| `send_message` | Push Messages 패널에 메시지 전송 |

## 시작하기

### 1. 브랜드 웹페이지 제작 (✏️ 핵심 수정 파일)

`index.html`을 팀의 브랜드 페이지로 교체하세요. 현재는 placeholder가 들어 있습니다.

GitHub Pages 주소: `https://{GitHub 유저명}.github.io/lumei-lamp/`

**제품 정보도 함께 수정** — `virtual.html` 안의 `<script id="product-info">` 블록을 팀 기획에 맞게 편집하세요.
```json
{
  "name": "LUMEI",
  "tagline": "빛이 피사체를 찾아가는 조명입니다.",
  "description": "...",
  "features": ["물체 자동 추적", "..."],
  "spec": { "battery": "8시간", "price": "79,000원" }
}
```
Claude가 제품 정보를 물어보면 이 JSON을 읽어서 답합니다.

### 2. GitHub Pages 활성화

레포 Settings → Pages → Branch: main, folder: / (root)

`virtual.html` 접속 주소:
`https://{GitHub 유저명}.github.io/lumei-lamp/virtual.html`

### 3. Worker URL 설정

`virtual.html` 안의 `MCP_WORKER_URL`을 자신의 Workers 주소로 교체하세요.
```js
const MCP_WORKER_URL = "https://lumei-lamp.{계정명}.workers.dev";
```

`src/index.ts` 안의 `VIRTUAL_DEVICE_URL`도 교체하세요.
```ts
const VIRTUAL_DEVICE_URL =
  "https://{GitHub 유저명}.github.io/lumei-lamp/virtual.html";
```

### 4. claude.ai 커넥터 연결

Settings → Integrations → Add custom integration
URL: `https://lumei-lamp.{계정명}.workers.dev/mcp`

## 사용 예시 (Claude에게 말하기)

- "lumei 전원 켜줘"
- "포커스 모드로 바꿔줘"
- "밝기 80으로 올려줘"
- "물체 추적 켜줘"
- "지금 상태 알려줘"
- "배터리 얼마나 남았어?"
- "조명에 '안녕하세요!' 메시지 보내줘"
- "이 제품 어떤 제품이야?"
