# White Socks Only Webtoons

세로 스크롤 웹툰을 보는 정적 사이트. 라이브러리 없이 HTML/CSS/JS 뿐입니다.

```
/                     작품 목록
└── /BestFriend/      웹툰 뷰어
```

## 구조

```
index.html              작품 목록 (카드 + 읽던 진행도 표시)
BestFriend/
  index.html            뷰어
  cover.jpg             목록용 썸네일 480x480
  WSOWT1~4.jpg          본편 (저장소에 올라감)
  WSOWT1~4.png          원본 (.gitignore 로 제외, 로컬 보관)
```

## 뷰어 동작

- **모바일**: 화면 꽉 채움, 폭 조절 없음
- **PC**: 우하단 `− 800px +` 로 7단계 폭 조절 (560~1080px), 키보드 `-` / `+`.
  설정은 기억되고, 폭을 바꿔도 읽던 지점이 유지됨
- 이미지 lazy loading + `width`/`height` 로 높이 선점 → 스크롤 튐 없음
- 읽던 위치 자동 저장 (`wt:<작품>:pos`), 목록 카드에 진행도 표시
- 좌상단 `← 목록`, 상단 읽기 진행 바

## 작품 추가하기

1. `<이름>/` 폴더를 만들고 `BestFriend/index.html` 을 복사한다.
2. 복사본에서 두 군데를 고친다.

   ```js
   var SLUG = '<이름>';   // 읽던 위치 저장 키. 작품마다 달라야 함
   ```

   ```html
   <img src="<파일>.jpg" width="1080" height="10800" alt="1" loading="lazy" decoding="async">
   ```

   **`width` / `height` 에 원본 픽셀 크기를 반드시 넣을 것.** 로딩 전에도 자리를 잡아
   스크롤이 튀지 않게 하는 값이라 틀리면 화면이 밀린다.

3. 루트 `index.html` 의 `.list` 안에 카드를 추가한다.

   ```html
   <a class="card" href="<이름>/">
     <div class="thumb">
       <img src="<이름>/cover.jpg" alt="" width="480" height="480">
       <div class="bar"><i data-progress="<이름>"></i></div>
     </div>
     <div class="meta">
       <h2><제목></h2>
       <p>N화 · 전편</p>
     </div>
   </a>
   ```

## 이미지 준비

원본 PNG는 용량이 커서 (29MB) JPEG 품질 85로 변환해 올립니다 (4.9MB, 83% 감소).
해상도는 1080px 폭 그대로이고 육안 차이는 없습니다.

```powershell
Add-Type -AssemblyName System.Drawing
$dir = "BestFriend"; $quality = 85
$codec = [System.Drawing.Imaging.ImageCodecInfo]::GetImageEncoders() |
         Where-Object { $_.MimeType -eq 'image/jpeg' }
$params = New-Object System.Drawing.Imaging.EncoderParameters 1
$params.Param[0] = New-Object System.Drawing.Imaging.EncoderParameter(
    [System.Drawing.Imaging.Encoder]::Quality, [int64]$quality)
foreach ($f in Get-ChildItem "$dir\*.png") {
    $img = [System.Drawing.Image]::FromFile($f.FullName)
    try { $img.Save((Join-Path $dir ($f.BaseName + '.jpg')), $codec, $params) }
    finally { $img.Dispose() }
}
```

크기 확인:

```powershell
Add-Type -AssemblyName System.Drawing
Get-ChildItem *.jpg | ForEach-Object {
  $i = [System.Drawing.Image]::FromFile($_.FullName)
  "$($_.Name): $($i.Width) x $($i.Height)"; $i.Dispose()
}
```

## 로컬에서 보기

```bash
python -m http.server 8777
```

`http://127.0.0.1:8777/` 접속. `file://` 로 열면 하위 경로 링크가 동작하지 않습니다.

## 배포

`main` 에 push 하면 GitHub Pages(Deploy from a branch, `main` / root)가 자동 반영합니다.
