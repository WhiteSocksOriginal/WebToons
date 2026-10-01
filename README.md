# WSOWT 웹툰 뷰어

세로로 긴 이미지 4장(WSOWT1~4.png)을 끊김 없이 이어 붙여 보여주는 정적 뷰어입니다.

- `index.html` 하나만 있으면 동작 (라이브러리 없음)
- 모바일: 화면 꽉 채움 / PC: 800px(기본) ↔ 1080px 토글
- 이미지 lazy loading + 미리 높이 확보 → 스크롤 튐 없음
- 읽던 위치 자동 저장, 상단 진행 바

## GitHub Pages 배포

1. GitHub에서 **public** 저장소를 새로 만든다 (예: `webtoon`). README 등 초기 파일은 체크하지 말 것.
2. 이 폴더에서:

   ```bash
   git remote add origin https://github.com/<아이디>/<저장소>.git
   git push -u origin main
   ```

3. 저장소 → **Settings → Pages** → Source를 **Deploy from a branch**,
   Branch를 **main / (root)** 로 지정하고 Save.
4. 1~2분 뒤 `https://<아이디>.github.io/<저장소>/` 에서 열린다.

## 이미지 교체 / 추가

`index.html`의 `<img>` 목록만 고치면 된다. **`width`/`height` 속성에 원본 픽셀 크기를
반드시 넣어야** 로딩 전에도 자리를 잡아 스크롤이 튀지 않는다.

```html
<img src="WSOWT5.png" width="1080" height="9000" alt="5" loading="lazy" decoding="async">
```

원본 크기 확인(PowerShell):

```powershell
Add-Type -AssemblyName System.Drawing
Get-ChildItem *.png | ForEach-Object {
  $i = [System.Drawing.Image]::FromFile($_.FullName)
  "$($_.Name): $($i.Width) x $($i.Height)"; $i.Dispose()
}
```

## 현재 이미지

| 파일 | 크기 | 용량 |
|---|---|---|
| WSOWT1.png | 1080 × 10800 | 9.0 MB |
| WSOWT2.png | 1080 × 10800 | 7.4 MB |
| WSOWT3.png | 1080 × 10305 | 7.4 MB |
| WSOWT4.png | 1080 × 12000 | 5.3 MB |

합계 약 29 MB. lazy loading 덕분에 처음부터 다 받지는 않지만, 모바일 데이터로는
여전히 무거운 편이라 JPEG/WebP 변환(보통 1/5~1/10)을 권장.
