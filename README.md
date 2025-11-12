# Speech to Indian Sign Language (ISL) Converter

A web-based application that converts English speech and text into Indian Sign Language (ISL) video representations. The system supports text input, audio file upload, and real-time voice recording.

## Features

- **Text to ISL**: Convert written English text to ISL video
- **Speech to ISL**: Record audio directly or upload audio files
- **Multi-word Phrase Support**: Recognizes 1-3 word phrases from the dataset
- **Fingerspelling Fallback**: Unknown words are spelled letter-by-letter
- **Real-time Processing**: Instant conversion and video generation
- **Web-based Interface**: Easy-to-use responsive UI

## Project Structure

```
speech-to-isl-converter/
├── app.py                      # Main Flask application (basic version)
├── main.py                     # Enhanced Flask app with Stanford Parser
├── requirements.txt            # Python dependencies
├── setup_windows.ps1          # Windows setup script
├── NLP_videos.csv             # ISL video dataset mapping
├── static/
│   ├── css/
│   │   └── styles.css         # Custom styling
│   ├── js/
│   │   └── script.js          # Frontend JavaScript
│   └── videos/                # Generated ISL videos
├── templates/
│   └── index.html             # Main web interface
├── NLP_dataset/               # Processed video clips
├── yt_downloads/              # Downloaded source videos
├── uploads/                   # User uploaded files
└── temp/                      # Temporary processing files
```

## Prerequisites

### Required Software
- **Python 3.11** (recommended)
- **Java 11+** (for Stanford Parser in main.py)
- **FFmpeg** (for video processing)
- **yt-dlp** (for video downloads)

### System Requirements
- 4GB RAM minimum (8GB recommended)
- 2GB free disk space for video processing
- Internet connection (for initial video downloads)

## Installation

### Windows

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd speech-to-isl-converter
   ```

2. **Run the automated setup script**
   ```powershell
   powershell -ExecutionPolicy Bypass -File setup_windows.ps1
   ```

   The script will:
   - Verify Python installation
   - Install Python dependencies
   - Download NLTK data
   - Download Stanford Parser
   - Install FFmpeg (if not present)
   - Verify Java installation

### Linux/macOS

1. **Install system dependencies**
   
   **Ubuntu/Debian:**
   ```bash
   sudo apt-get update
   sudo apt-get install python3.11 python3-pip ffmpeg default-jdk portaudio19-dev
   pip install yt-dlp
   ```

   **macOS:**
   ```bash
   brew install python@3.11 ffmpeg openjdk portaudio
   pip install yt-dlp
   ```

2. **Install Python dependencies**
   ```bash
   pip install -r requirements.txt
   ```

3. **Download NLTK data**
   ```python
   python -c "import nltk; nltk.download('stopwords'); nltk.download('wordnet'); nltk.download('omw-1.4')"
   ```

4. **Download Stanford Parser** (for main.py)
   ```bash
   wget https://downloads.cs.stanford.edu/nlp/software/stanford-parser-4.2.0.zip
   unzip stanford-parser-4.2.0.zip
   mv stanford-parser-full-2020-11-17 stanford-parser-full
   ```

## Usage

### Starting the Application

**Basic Version (app.py):**
```bash
python app.py
```

**Enhanced Version with NLP (main.py):**
```bash
python main.py
```

The application will start on `http://127.0.0.1:5000`

### Using the Interface

1. **Text Input**
   - Navigate to the "Text Input" tab
   - Enter English text in the textarea
   - Click "Convert to ISL"

2. **Record Audio**
   - Navigate to the "Record Audio" tab
   - Click the microphone button to start recording
   - Click the stop button when finished
   - Audio will be automatically processed

3. **Upload Audio**
   - Navigate to the "Upload Audio" tab
   - Select an audio file (.wav, .mp3, etc.)
   - Click "Upload and Convert"

### Output

The system displays:
- **English Text**: Original or transcribed text
- **ISL Text**: Processed text in ISL word order
- **ISL Video**: Generated sign language video

## Dataset

The application uses `NLP_videos.csv` containing:
- **1000+ ISL signs** mapped to English words
- Video sources from YouTube and talkinghands.co.in
- Timestamp information for video clipping
- Support for multi-word phrases

### Dataset Format
```csv
Name,yt_name,Link,start_min,start_sec,end_min,end_sec
hello,hello,https://example.com/video.mp4,0,0,0,2
```

## Technical Details

### Text to ISL Conversion

The system performs:
1. **Punctuation removal**
2. **Stopword filtering** (articles, prepositions)
3. **Phrase matching** (3-word → 2-word → 1-word)
4. **Lemmatization** (main.py only)
5. **Fingerspelling fallback** for unknown words

### Video Processing Pipeline

1. **Download** source videos (cached locally)
2. **Cut** relevant segments using FFmpeg
3. **Resize** clips to uniform resolution
4. **Concatenate** clips into final ISL video
5. **Serve** video through web interface

### Enhanced Features (main.py)

- **Stanford Parser** for grammatical analysis
- **Word lemmatization** using NLTK
- **Similarity matching** for approximate word matching
- **ISL grammar structure** conversion

## Configuration

### Modifying Stopwords

Edit the `stopwords_set` in `app.py` or `main.py`:
```python
stopwords_set = set(['a', 'an', 'the', 'is', 'to', ...])
```

### Adjusting Video Quality

Modify FFmpeg parameters in the `cut_video()` function:
```python
clip.write_videofile(out_path, codec="libx264", audio_codec="aac", bitrate="1000k")
```

### Adding New Signs

Add entries to `NLP_videos.csv`:
```csv
new_word,video_name,video_url,start_min,start_sec,end_min,end_sec
```

## Troubleshooting

### Common Issues

1. **"Java not found" error**
   - Install Java 11+ from Oracle or OpenJDK
   - Ensure `java` is in system PATH

2. **"FFmpeg not found" error**
   - Run setup script again (Windows)
   - Install via package manager (Linux/macOS)

3. **"Video download failed"**
   - Check internet connection
   - Verify yt-dlp is installed
   - Some videos may be region-restricted

4. **"Speech recognition failed"**
   - Ensure clear audio input
   - Check microphone permissions
   - Audio file format should be supported

5. **"Stanford Parser initialization failed"**
   - Verify Java installation
   - Check stanford-parser.jar exists
   - Use app.py instead of main.py

### Performance Optimization

- **Pre-download videos**: Run initial conversion to cache videos
- **Clear temp files**: Regularly clean `temp/` directory
- **Reduce video quality**: Adjust FFmpeg encoding settings
- **Use SSD storage**: For faster video processing

## API Endpoints

### POST `/process_text`
Convert text to ISL
```json
{
  "text": "hello world"
}
```

### POST `/process_audio`
Convert uploaded audio to ISL
```json
{
  "audio": "<audio_file>"
}
```

### POST `/record_audio`
Convert recorded audio to ISL
```json
{
  "audio": "<audio_blob>"
}
```

## Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request

### Adding New ISL Signs

1. Add video URL to `NLP_videos.csv`
2. Include accurate timestamps
3. Test conversion accuracy
4. Update documentation

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- ISL video dataset from [TalkingHands](https://talkinghands.co.in/)
- Stanford NLP Group for Stanford Parser
- NLTK project for NLP tools
- YouTube creators for educational sign language content

## Future Enhancements

- [ ] Real-time webcam ISL recognition
- [ ] Support for more Indian regional sign languages
- [ ] Improved grammar structure conversion
- [ ] Mobile app version
- [ ] Offline mode with pre-downloaded videos
- [ ] User-contributed sign dictionary
- [ ] Multi-language support (Hindi, Tamil, etc.)

## Support

For issues and questions:
- Create an issue on GitHub
- Check existing documentation
- Review troubleshooting section

## Version History

- **v1.0.0** - Initial release with basic text/speech to ISL
- **v1.1.0** - Added Stanford Parser support (main.py)
- **v1.2.0** - Multi-word phrase recognition
- **v1.3.0** - Enhanced video processing pipeline

---

**Note**: This application requires internet connectivity for initial video downloads. Videos are cached locally after first use for faster subsequent conversions.
