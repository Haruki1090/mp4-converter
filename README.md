# MP4 to Audio Converter

コマンドラインでMP4動画を音声ファイルに変換するツール。FFmpegベースの軽量で高速な変換関数。

## 📋 目次

- [セットアップ](#セットアップ)
- [使用方法](#使用方法)
- [コマンド一覧](#コマンド一覧)
- [実行例](#実行例)
- [トラブルシューティング](#トラブルシューティング)
- [技術詳細](#技術詳細)

## 🚀 セットアップ

### 1. FFmpegのインストール

```bash
# Homebrewでインストール
brew install ffmpeg
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
EOF

# 設定を反映
source ~/.zshrc
```

## 📖 使用方法

### 基本的な使い方

```bash
# 単一ファイルを変換
mp4tomp3 video.mp4

# 複数ファイルを変換
mp4tomp3 video1.mp4 video2.mp4 video3.mp4

# ワイルドカードで一括変換
mp4tomp3 *.mp4
```

### ディレクトリ移動してから変換

```bash
# 変換したいディレクトリに移動
cd ~/Desktop/videos

# そのディレクトリ内のすべてのMP4を変換
mp4tomp3 *.mp4
```

## 🛠 コマンド一覧

| コマンド | 出力形式 | 音質 | 用途 |
|---------|---------|------|------|
| `mp4tomp3` | MP3 | 192kbps | 標準的な音楽変換 |
| `mp4tomp3hq` | MP3 | 320kbps | 高音質が必要な場合 |
| `mp4towav` | WAV | 無圧縮 | 音楽制作・編集用 |
| `mp4toaac` | AAC | 256kbps | Apple製品での再生用 |

### 音質の目安

- **192kbps MP3**: 一般的な音楽鑑賞に十分
- **320kbps MP3**: 高音質、ファイルサイズ大
- **WAV**: 無圧縮、最高音質、ファイルサイズ非常に大
- **256kbps AAC**: 効率的な圧縮、Apple推奨

## 💡 実行例

### 例1: Podcastの音声抽出

```bash
cd ~/Downloads/podcasts
mp4tomp3 *.mp4
```

### 例2: 音楽動画の高音質変換

```bash
mp4tomp3hq "音楽動画.mp4"
```

### 例3: 音楽制作用の無圧縮変換

```bash
mp4towav recording.mp4
```

### 例4: iPhone用の効率的な変換

```bash
mp4toaac interview.mp4
```

## 🔧 トラブルシューティング

### FFmpegが見つからない場合

```bash
# FFmpegがインストールされているか確認
ffmpeg -version

# インストールされていない場合
brew install ffmpeg
```

### 関数が認識されない場合

```bash
# .zshrcを再読み込み
source ~/.zshrc

# 新しいターミナルを開く
```

### 権限エラーが出る場合

```bash
# ファイルの権限を確認
ls -la video.mp4

# 読み取り権限がない場合
chmod 644 video.mp4
```

### バックアップから復元

```bash
# 元の設定に戻す
cp ~/Desktop/zshrc_backup_*.txt ~/.zshrc
source ~/.zshrc
```

## ⚙️ 技術詳細

### 使用しているFFmpegオプション

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

### オプションの説明

- `-i input.mp4`: 入力ファイル
- `-vn`: 映像を除外（音声のみ）
- `-acodec`: 音声コーデック指定
- `-ab`: 音声ビットレート
- `-loglevel error`: エラーメッセージのみ表示

### パフォーマンス

- **変換速度**: 元動画の2-10倍速
- **メモリ使用量**: 数十MB程度
- **CPU使用率**: 変換中は高負荷、完了後は0

## 📁 ファイル構成

```
~/
├── .zshrc                 # 関数定義ファイル
├── Desktop/
│   └── zshrc_backup.txt   # バックアップファイル
└── [変換対象ディレクトリ]/
    ├── video1.mp4         # 元ファイル
    ├── video1.mp3         # 変換後ファイル
    ├── video2.mp4
    └── video2.mp3
```

## 🚨 注意事項

- **上書き確認なし**: 同名ファイルがある場合は自動で上書き
- **拡張子チェック**: `.mp4`ファイルのみ処理
- **エラーハンドリング**: FFmpegのエラーは標準エラー出力に表示
- **ファイルサイズ**: WAV形式は非常に大きなファイルになる可能性

## 🔄 更新履歴

- **v1.0**: 基本的なMP3変換機能
- **v1.1**: 高音質オプション追加
- **v1.2**: WAV・AAC形式対応
- **v1.3**: エラーハンドリング改善

## 📞 サポート

問題が発生した場合：

1. FFmpegが正しくインストールされているか確認
2. ファイルの権限を確認
3. .zshrcの設定を確認
4. 新しいターミナルセッションで試行

---

**作成日**: $(date +%Y-%m-%d)  
**動作環境**: macOS (zsh)  
**依存関係**: FFmpeg, Homebrew


