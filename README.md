# WSOWT 웹툰 뷰어

세로로 긴 이미지 4장을 끊김 없이 이어 붙여 보여주는 정적 뷰어입니다.
`index.html` 하나로 동작하며 라이브러리가 없습니다.

- **모바일**: 화면 꽉 채움 (폭 조절 없음)
- **PC**: 우하단 `− 800px +` 로 7단계 폭 조절, 키보드 `-` / `+` 도 동작. 선택은 기억됨
- 이미지 lazy loading + 높이 선점 → 스크롤 튐 없음
- 읽던 위치 자동 저장, 상단 진행 바, 다크 배경

## GitHub Pages 배포

1. GitHub에서 **public** 저장소를 새로 만든다 (예: `webtoon`). 초기 파일(README 등)은 체크하지 말 것.
2. 이 폴더에서:

   ```bash
   git remote add origin https://github.com/<아이디>/<저장소>.git
   git push -u origin main
   ```

3. 저장소 → **Settings → Pages** → Source **Deploy from a branch**,
   Branch **main / (root)** → Save
4. 1~2분 뒤 `https://<아이디>.github.io/<저장소>/` 에서 열린다.

## 이미지 파일

뷰어는 **`.jpg`** 를 사용합니다. 원본 `.png` 는 로컬 보관용이며 `.gitignore` 로 제외돼
저장소에는 올라가지 않습니다.

| 파일 | 크기 | PNG 원본 | JPG (품질 85) |
|---|---|---|---|
| WSOWT1 | 1080 × 10800 | 9.0 MB | 1.3 MB |
| WSOWT2 | 1080 × 10800 | 7.4 MB | 1.4 MB |
| WSOWT3 | 1080 × 10305 | 7.4 MB | 1.3 MB |
| WSOWT4 | 1080 × 12000 | 5.3 MB | 0.9 MB |
| **합계** | | **29 MB** | **4.9 MB** |

### PNG → JPG 재변환

원본 PNG를 수정했다면 아래를 실행해 `.jpg` 를 다시 만든 뒤 커밋한다 (PowerShell):

```powershell
Add-Type -AssemblyName System.Drawing
$dir = $PSScriptRoot; $quality = 85
$codec = [System.Drawing.Imaging.ImageCodecInfo]::GetImageEncoders() |
         Where-Object { $_.MimeType -eq 'image/jpeg' }
$params = New-Object System.Drawing.Imaging.EncoderParameters 1
$params.Param[0] = New-Object System.Drawing.Imaging.EncoderParameter(
    [System.Drawing.Imaging.Encoder]::Quality, [int64]$quality)
foreach ($f in Get-ChildItem "$dir\WSOWT*.png") {
    $img = [System.Drawing.Image]::FromFile($f.FullName)
    try { $img.Save((Join-Path $dir ($f.BaseName + '.jpg')), $codec, $params) }
    finally { $img.Dispose() }
}
```

### 이미지 추가 / 교체

`index.html` 의 `<img>` 목록만 고치면 된다. **`width` / `height` 에 원본 픽셀 크기를
반드시 넣어야** 로딩 전에도 자리를 잡아 스크롤이 튀지 않는다.

```html
<img src="WSOWT5.jpg" width="1080" height="9000" alt="5" loading="lazy" decoding="async">
```

크기 확인:

```powershell
Add-Type -AssemblyName System.Drawing
Get-ChildItem *.jpg | ForEach-Object {
  $i = [System.Drawing.Image]::FromFile($_.FullName)
  "$($_.Name): $($i.Width) x $($i.Height)"; $i.Dispose()
}
```
