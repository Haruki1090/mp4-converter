# メディア変換ツール

コマンドラインでMP4動画を音声ファイルに変換、PDFを画像ファイルに変換するツール。FFmpegとpoppler-utilsベースの軽量で高速な変換関数。

## 📋 目次

- [セットアップ](#セットアップ)
- [MP4音声変換](#mp4音声変換)
- [PDF画像変換](#pdf画像変換)
- [コマンド一覧](#コマンド一覧)
- [実行例](#実行例)
- [トラブルシューティング](#トラブルシューティング)
- [技術詳細](#技術詳細)

## 🚀 セットアップ

### 1. 必要なツールのインストール

```bash
# FFmpeg（MP4変換用）
brew install ffmpeg

# poppler-utils（PDF変換用）
brew install poppler
```

### 2. 変換関数の追加

```bash
# バックアップ作成（推奨）
cp ~/.zshrc ~/Desktop/zshrc_backup_$(date +%Y%m%d).txt

# 関数を.zshrcに追加
cat >> ~/.zshrc << 'EOF'

# === MP4変換関数 ===
mp4tomp3() {
    for file in "$@"; do
        if [[ "$file" == *.mp4 ]]; then
            output="${file%.*}.mp3"
            echo "変換中: $file → $output"
            ffmpeg -i "$file" -vn -acodec mp3 -ab 192k "$output" -loglevel error
            echo "✅ 完了: $output"
        fi
    done
}

mp4tomp3hq() {
    for file in "$@"; do
        if [[ "$file" == *.mp4 ]]; then
            output="${file%.*}.mp3"
            echo "変換中: $file → $output (高音質)"
            ffmpeg -i "$file" -vn -acodec mp3 -ab 320k "$output" -loglevel error
            echo "✅ 完了: $output"
        fi
    done
}

mp4towav() {
    for file in "$@"; do
        if [[ "$file" == *.mp4 ]]; then
            output="${file%.*}.wav"
            echo "変換中: $file → $output (WAV)"
            ffmpeg -i "$file" -vn -acodec pcm_s16le "$output" -loglevel error
            echo "✅ 完了: $output"
        fi
    done
}

mp4toaac() {
    for file in "$@"; do
        if [[ "$file" == *.mp4 ]]; then
            output="${file%.*}.aac"
            echo "変換中: $file → $output (AAC)"
            ffmpeg -i "$file" -vn -acodec aac -ab 256k "$output" -loglevel error
            echo "✅ 完了: $output"
        fi
    done
}

# === PDF変換関数 ===
pdftopng() {
    for file in "$@"; do
        if [[ "$file" == *.pdf ]]; then
            basename="${file%.*}"
            echo "変換中: $file → ${basename}_page*.png"
            pdftoppm -png -r 150 "$file" "${basename}_page"
            echo "✅ 完了: ${basename}_page-*.png"
        fi
    done
}

pdftopnghq() {
    for file in "$@"; do
        if [[ "$file" == *.pdf ]]; then
            basename="${file%.*}"
            echo "変換中: $file → ${basename}_page*.png (高品質)"
            pdftoppm -png -r 300 "$file" "${basename}_page"
            echo "✅ 完了: ${basename}_page-*.png"
        fi
    done
}

pdftojpg() {
    for file in "$@"; do
        if [[ "$file" == *.pdf ]]; then
            basename="${file%.*}"
            echo "変換中: $file → ${basename}_page*.jpg"
            pdftoppm -jpeg -r 150 "$file" "${basename}_page"
            echo "✅ 完了: ${basename}_page-*.jpg"
        fi
    done
}

pdftojpghq() {
    for file in "$@"; do
        if [[ "$file" == *.pdf ]]; then
            basename="${file%.*}"
            echo "変換中: $file → ${basename}_page*.jpg (高品質)"
            pdftoppm -jpeg -r 300 -jpegopt quality=95 "$file" "${basename}_page"
            echo "✅ 完了: ${basename}_page-*.jpg"
        fi
    done
}
EOF

# 設定を反映
source ~/.zshrc
```

## 🎵 MP4音声変換

### 基本的な使い方

```bash
# 単一ファイルを変換
mp4tomp3 video.mp4

# 複数ファイルを変換
mp4tomp3 video1.mp4 video2.mp4 video3.mp4

# ワイルドカードで一括変換
mp4tomp3 *.mp4
```

### 高音質変換

```bash
# 320kbpsの高音質MP3
mp4tomp3hq music_video.mp4

# 無圧縮WAV（音楽制作用）
mp4towav recording.mp4

# AAC形式（Apple推奨）
mp4toaac interview.mp4
```

## 🖼 PDF画像変換

### 基本的な使い方

```bash
# PDFを各ページPNG画像に変換
pdftopng presentation.pdf
# → presentation_page-01.png, presentation_page-02.png... が生成

# 高品質PNG（300dpi）
pdftopnghq detailed_document.pdf

# JPEG形式で変換（ファイルサイズ重視）
pdftojpg slides.pdf

# 高品質JPEG
pdftojpghq important_slides.pdf
```

### Notion活用例

```bash
# スライドPDFを各ページ画像に変換してNotionに貼り付け
cd ~/Desktop/presentations
pdftopng "プレゼン資料.pdf"
# 生成されたpng画像をNotionページに個別に貼り付け可能
```

## 🛠 コマンド一覧

### MP4音声変換

| コマンド | 出力形式 | 音質 | 用途 |
|---------|---------|------|------|
| `mp4tomp3` | MP3 | 192kbps | 標準的な音楽変換 |
| `mp4tomp3hq` | MP3 | 320kbps | 高音質が必要な場合 |
| `mp4towav` | WAV | 無圧縮 | 音楽制作・編集用 |
| `mp4toaac` | AAC | 256kbps | Apple製品での再生用 |

### PDF画像変換

| コマンド | 出力形式 | 解像度 | 用途 |
|---------|---------|--------|------|
| `pdftopng` | PNG | 150dpi | 標準的な資料変換 |
| `pdftopnghq` | PNG | 300dpi | 高品質が必要な場合 |
| `pdftojpg` | JPEG | 150dpi | ファイルサイズ重視 |
| `pdftojpghq` | JPEG | 300dpi | 高品質・ファイルサイズ効率 |

## 💡 実行例

### 例1: Podcastの音声抽出

```bash
cd ~/Downloads/podcasts
mp4tomp3 *.mp4
# すべてのMP4ファイルを192kbps MP3に変換
```

### 例2: プレゼン資料をNotionページ用に変換

```bash
cd ~/Documents/presentations
pdftopng "月次報告会.pdf"
# 各スライドを個別のPNG画像として出力
# → Notionに1ページずつ貼り付け可能
```

### 例3: 音楽動画の高音質変換

```bash
mp4tomp3hq "ライブ映像.mp4"
# 320kbpsの高音質MP3で変換
```

### 例4: 資料の高品質画像変換

```bash
pdftopnghq "詳細仕様書.pdf"
# 300dpiの高解像度PNG画像で変換
# 細かい文字や図表もクリアに
```

### 例5: iPhone用の効率的な変換

```bash
mp4toaac interview.mp4
# AAC形式でファイルサイズを抑制
```

### 例6: 複数ファイルの一括変換

```bash
# MP4一括変換
mp4tomp3 *.mp4

# PDF一括変換
pdftopng *.pdf

# 特定のファイルのみ
pdftojpg slide1.pdf slide2.pdf slide3.pdf
```

## 🔧 トラブルシューティング

### 依存ツールの確認

```bash
# FFmpegが正しくインストールされているか確認
ffmpeg -version

# poppler-utilsが正しくインストールされているか確認
pdftoppm -h

# インストールされていない場合
brew install ffmpeg poppler
```

### 関数が認識されない場合

```bash
# .zshrcを再読み込み
source ~/.zshrc

# 新しいターミナルを開く
# または関数が正しく追加されているか確認
cat ~/.zshrc | grep -A 5 "MP4変換関数"
```

### 権限エラーが出る場合

```bash
# ファイルの権限を確認
ls -la video.mp4 document.pdf

# 読み取り権限がない場合
chmod 644 video.mp4 document.pdf
```

### PDF変換でエラーが出る場合

```bash
# PDFが破損していないか確認
pdf info document.pdf

# 古いPDFの場合、ImageMagickを試す
convert -density 150 document.pdf page_%02d.png
```

### バックアップから復元

```bash
# 元の設定に戻す
cp ~/Desktop/zshrc_backup_*.txt ~/.zshrc
source ~/.zshrc
```

## ⚙️ 技術詳細

### MP4変換で使用しているFFmpegオプション

```bash
# MP3 (192kbps)
ffmpeg -i input.mp4 -vn -acodec mp3 -ab 192k output.mp3

# MP3 (320kbps)
ffmpeg -i input.mp4 -vn -acodec mp3 -ab 320k output.mp3

# WAV (無圧縮)
ffmpeg -i input.mp4 -vn -acodec pcm_s16le output.wav

# AAC (256kbps)
ffmpeg -i input.mp4 -vn -acodec aac -ab 256k output.aac
```

### PDF変換で使用しているpoppler-utilsオプション

```bash
# PNG (150dpi)
pdftoppm -png -r 150 input.pdf output_page

# PNG (300dpi)
pdftoppm -png -r 300 input.pdf output_page

# JPEG (150dpi)
pdftoppm -jpeg -r 150 input.pdf output_page

# JPEG (300dpi, 高品質)
pdftoppm -jpeg -r 300 -jpegopt quality=95 input.pdf output_page
```

### オプションの説明

**FFmpeg:**
- `-i input.mp4`: 入力ファイル
- `-vn`: 映像を除外（音声のみ）
- `-acodec`: 音声コーデック指定
- `-ab`: 音声ビットレート
- `-loglevel error`: エラーメッセージのみ表示

**pdftoppm:**
- `-png`/`-jpeg`: 出力形式指定
- `-r`: 解像度（dpi）
- `-jpegopt quality=95`: JPEG品質設定

### パフォーマンス

**MP4変換:**
- **変換速度**: 元動画の2-10倍速
- **メモリ使用量**: 数十MB程度
- **CPU使用率**: 変換中は高負荷

**PDF変換:**
- **変換速度**: 1ページあたり1-3秒
- **メモリ使用量**: ページ数×数MB
- **CPU使用率**: 軽負荷

### ファイル命名規則

**MP4変換:**
```
input.mp4 → input.mp3/wav/aac
```

**PDF変換:**
```
document.pdf → document_page-01.png, document_page-02.png...
```

## 📁 ファイル構成例

```
~/Documents/work/
├── presentation.pdf           # 元ファイル
├── presentation_page-01.png   # 1ページ目
├── presentation_page-02.png   # 2ページ目
├── presentation_page-03.png   # 3ページ目
├── video.mp4                  # 元ファイル
└── video.mp3                  # 変換後音声
```

## 🎯 使用シーン別推奨コマンド

### Notion資料作成
```bash
pdftopng slides.pdf        # 標準品質、適度なファイルサイズ
pdftojpg large_doc.pdf     # ファイルサイズ重視
```

### 音楽・Podcast
```bash
mp4tomp3 podcast.mp4       # 標準品質、ファイルサイズ効率
mp4tomp3hq music.mp4       # 音質重視
```

### プレゼン・会議資料
```bash
pdftopnghq important.pdf   # 高品質、文字もクリア
```

### アーカイブ・バックアップ
```bash
mp4towav recording.mp4     # 無圧縮、最高音質
pdftopnghq archive.pdf     # 高解像度保存
```

## 🚨 注意事項

- **上書き確認なし**: 同名ファイルがある場合は自動で上書き
- **拡張子チェック**: `.mp4`、`.pdf`ファイルのみ処理
- **ファイルサイズ**: WAV形式とPNG高品質は大きなファイルになる可能性
- **日本語ファイル名**: 対応済み、スペースを含むファイル名も処理可能

## 🔄 更新履歴

- **v1.0**: 基本的なMP3変換機能
- **v1.1**: 高音質オプション追加
- **v1.2**: WAV・AAC形式対応
- **v1.3**: エラーハンドリング改善
- **v2.0**: PDF画像変換機能追加
- **v2.1**: 高品質PDF変換オプション追加

## 📞 サポート

問題が発生した場合：

1. **依存ツール確認**: FFmpegとpoppler-utilsが正しくインストールされているか
2. **ファイル権限確認**: 読み取り権限があるか
3. **設定確認**: .zshrcの関数定義を確認
4. **新規セッション**: 新しいターミナルセッションで試行
5. **ファイル形式確認**: 対応形式（MP4、PDF）かどうか

---

**作成日**: 2025-06-08  
**動作環境**: macOS (zsh)  
**依存関係**: FFmpeg, poppler-utils, Homebrew  
**想定用途**: メディア変換、Notion資料作成、音声抽出
