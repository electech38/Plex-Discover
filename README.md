# 🎬 Plex-RD Auto Downloader

## Tổng quan

Phần mềm tự động download phim từ Plex Watchlist → Real-Debrid → Local Storage → Auto refresh Plex library.

**Dựa trên**: RD Torrent Downloader (with license system + online tokens)  
**Tích hợp thêm**: Plex API, background monitoring, system tray

---

## ✨ Features

- ✅ **Background monitoring** Plex watchlist (polling every 5-10s)
- ✅ **Auto-download workflow**: Watchlist → Search → Upload → Download → Refresh
- ✅ **Quality filters**: 1080p, 4K, size range (1-100GB+)
- ✅ **System tray**: Chạy nền, minimize to tray
- ✅ **Hotkey**: Ctrl+F8 để exit
- ✅ **License system**: HWID-based activation
- ✅ **Online tokens**: Real-Debrid tokens từ Google Drive

---

## 📋 Project Structure

```
plex-rd-downloader/
├── plex_rd_downloader.py         # Main app
├── plex_config_dialog.py         # Plex settings dialog
├── license_system.py              # License validation
├── activation_dialog.py           # License activation UI
├── secure_token_manager.py        # Online token fetcher
├── license_key_generator.py       # Key generator (seller only)
├── requirements_plex_rd.txt       # Dependencies
├── build_plex_rd.py              # Build script
├── PLEX_RD_USER_GUIDE.md         # User manual
└── README.md                      # This file
```

---

## 🚀 Quick Start

### For Users:

1. Run `PlexRD_AutoDownloader.exe`
2. Activate license
3. Configure Plex settings
4. Add movies to Plex watchlist
5. Done! App auto-downloads

### For Developers:

```bash
# Install dependencies
pip install -r requirements_plex_rd.txt

# Run from source
python plex_rd_downloader.py

# Build executable
python build_plex_rd.py
```

---

## 🔧 Architecture

### Components:

**1. PlexService** - Plex API wrapper
- `get_watchlist()` - Fetch watchlist items
- `refresh_library()` - Refresh library section
- `remove_from_watchlist()` - Remove item

**2. PlexMonitoringService** - Background thread
- Polls watchlist every N seconds
- Processes new items
- Emits signals for UI updates

**3. MainWindow** - UI + System Tray
- Minimal UI (activity log + settings)
- System tray icon
- Global hotkey (Ctrl+F8)

**4. License System** (from RD Downloader)
- HWID-based validation
- Activation dialog
- Renewal warnings

**5. Token Manager** (from RD Downloader)
- Online token fetching
- Round-robin token rotation

### Workflow:

```
┌─────────────────────────────────────────────────┐
│  User adds to Plex Watchlist                     │
└────────────┬────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────┐
│  PlexMonitoringService (background thread)       │
│  - Polls every 10s                               │
│  - Fetches watchlist                             │
│  - Detects new items                             │
└────────────┬────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────┐
│  Process Item                                    │
│  1. Get IMDb ID (from Plex or via TMDB)         │
│  2. Search torrents (Torrentio)                  │
│  3. Filter by quality & size                     │
│  4. Upload magnet to Real-Debrid                 │
│  5. Wait for RD download                         │
│  6. Download file to local path                  │
│  7. Refresh Plex library                         │
└────────────┬────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────┐
│  Done! Movie ready in Plex                       │
└─────────────────────────────────────────────────┘
```

---

## ⚙️ Configuration

**plex_config.json:**
```json
{
  "plex_server_url": "http://192.168.1.100:32400",
  "plex_token": "YOUR_PLEX_TOKEN",
  "download_path": "C:\\Movies",
  "library_section_id": "1",
  "resolution_1080": true,
  "resolution_4k": false,
  "min_size_gb": 1,
  "max_size_gb": 100,
  "polling_interval": 10
}
```

**processed_watchlist.json:**
```json
["Avatar_2009", "Matrix_1999"]
```
→ Tracks processed items để không download lại

---

## 🔑 License System

Same as RD Downloader:
- HWID-based activation
- 1 year validity
- Renewal warnings at 30 days
- Grace period support

**Generate keys:**
```bash
python license_key_generator.py
```

---

## 🌐 Online Tokens

Tokens stored on Google Drive, fetched at app start:
- URL: https://drive.google.com/file/d/1ELkQzJrZP587iQFmqYWUT2K_AxBGcLlV/view
- Format: One token per line
- Fallback tokens in code if offline

---

## 🏗️ Build & Deploy

### Build EXE:

```bash
python build_plex_rd.py
```

Output: `dist/PlexRD_AutoDownloader.exe`

### Distribute:

- Single EXE file
- No additional files needed
- Users configure on first run

---

## 🎯 Differences from RD Downloader

| Feature | RD Downloader | Plex-RD Auto |
|---------|---------------|--------------|
| Input | Manual search | Plex watchlist |
| Workflow | Manual download | Auto download |
| UI | Dual-table search UI | Minimal status UI |
| Background | No | Yes (system tray) |
| Plex integration | No | Yes |
| License | Yes | Yes |
| Online tokens | Yes | Yes |

---

## 📝 TODO / Future Enhancements

- [ ] TV Shows support (seasons, episodes)
- [ ] Multiple watchlist users
- [ ] Custom naming templates
- [ ] Webhook notifications (Discord, Telegram)
- [ ] Web UI (instead of PyQt)
- [ ] Docker support
- [ ] Radarr/Sonarr integration
- [ ] Auto-remove from watchlist after download

---

## 🐛 Known Issues

1. **Only supports movies** - TV shows require season/episode logic
2. **Simple filename** - Just "Title (Year).mkv"
3. **No progress in tray** - Only status notifications
4. **Single Plex user** - Watchlist từ 1 user token

---

## 📚 API References

**Plex API:**
- Watchlist: `https://metadata.provider.plex.tv/library/sections/watchlist/all`
- Refresh: `{server}/library/sections/{id}/refresh`

**Real-Debrid API:**
- Docs: https://api.real-debrid.com/

**Torrentio:**
- Endpoint: `https://torrentio.strem.fun/stream/movie/{imdb_id}.json`

**TMDB API:**
- Search: `https://api.themoviedb.org/3/search/movie`
- Details: `https://api.themoviedb.org/3/movie/{id}`

---

## 💡 Development Tips

### Testing without Plex:

Comment out Plex calls and mock data:
```python
def get_watchlist(self):
    return [
        {'title': 'Test Movie', 'year': '2023', 'imdb_id': 'tt1234567'}
    ]
```

### Debug mode:

Add verbose logging:
```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

### Skip license check:

For development only:
```python
# In MainWindow.__init__(), comment out license check
# valid, message, days_remaining = check_license()
```

---

## 🤝 Contributing

Improvements welcome:
1. Fork repo
2. Create feature branch
3. Test thoroughly
4. Submit PR

---

## 📄 License

Proprietary - Licensed software  
Contact seller for commercial use

---

## 🙏 Credits

**Based on:**
- plex_debrid (Plex integration concept)
- RD Torrent Downloader (license + token system)

**APIs:**
- Plex Media Server
- Real-Debrid
- TMDB
- Torrentio

---

## 📞 Support

For issues:
1. Check `PLEX_RD_USER_GUIDE.md`
2. Review logs
3. Contact seller

---

**Enjoy your automated Plex downloads! 🎬**
