# VsCode_Setting_Bug & DeBug

1. Soundfile can not read *.FLAC file in MacOS..... 
参考链接：[https://github.com/bastibe/python-soundfile/issues/237]
``` Python
Traceback (most recent call last):
  File "create_librispeech_metadata.py", line 126, in <module>
    main(args)
  File "create_librispeech_metadata.py", line 25, in main
    create_librispeech_metadata(librispeech_dir, librispeech_md_dir)   # excute function
  File "create_librispeech_metadata.py", line 39, in create_librispeech_metadata
    speakers_metadata)
  File "create_librispeech_metadata.py", line 116, in create_librispeech_dataframe
    length = len(sf.SoundFile(sound_path))
  File "/Users/Star/anaconda3/envs/asrEnv/lib/python3.6/site-packages/soundfile.py", line 629, in __init__
    self._file = self._open(file, mode_int, closefd)
  File "/Users/Star/anaconda3/envs/asrEnv/lib/python3.6/site-packages/soundfile.py", line 1184, in _open
    "Error opening {0!r}: ".format(self.name))
  File "/Users/Star/anaconda3/envs/asrEnv/lib/python3.6/site-packages/soundfile.py", line 1357, in _error_check
    raise RuntimeError(prefix + _ffi.string(err_str).decode('utf-8', 'replace'))
RuntimeError: Error opening '/Users/Star/Desktop/material/data/LibriSpeech/test-clean/61/70970/61-70970-0040.flac': File contains data in an unimplemented format.
```
* deBug
```Python
>>> import soundfile as sf
>>> sf.available_formats()
{'AIFF': 'AIFF (Apple/SGI)', 'AU': 'AU (Sun/NeXT)', 'AVR': 'AVR (Audio Visual Research)', 'CAF': 'CAF (Apple Core Audio File)', 'HTK': 'HTK (HMM Tool Kit)', 'SVX': 'IFF (Amiga IFF/SVX8/SV16)', 'MAT4': 'MAT4 (GNU Octave 2.0 / Matlab 4.2)', 'MAT5': 'MAT5 (GNU Octave 2.1 / Matlab 5.0)', 'MPC2K': 'MPC (Akai MPC 2k)', 'PAF': 'PAF (Ensoniq PARIS)', 'PVF': 'PVF (Portable Voice Format)', 'RAW': 'RAW (header-less)', 'RF64': 'RF64 (RIFF 64)', 'SD2': 'SD2 (Sound Designer II)', 'SDS': 'SDS (Midi Sample Dump Standard)', 'IRCAM': 'SF (Berkeley/IRCAM/CARL)', 'VOC': 'VOC (Creative Labs)', 'W64': 'W64 (SoundFoundry WAVE 64)', 'WAV': 'WAV (Microsoft)', 'NIST': 'WAV (NIST Sphere)', 'WAVEX': 'WAVEX (Microsoft)', 'WVE': 'WVE (Psion Series 3)', 'XI': 'XI (FastTracker 2)'}
Error: In Windows, FLAC is contained in the output resule....
>>> sf.available_subtypes(format='FLAC')
{'PCM_S8': 'Signed 8 bit PCM', 'PCM_16': 'Signed 16 bit PCM', 'PCM_24': 'Signed 24 bit PCM'}

>>> Also, FLAC files can be played in macOS.
>>> Details about conda and system(conda info)
```

           active environment : asrEnv
          active env location : /Users/Star/anaconda3/envs/asrEnv
                  shell level : 2
             user config file : /Users/Star/.condarc
       populated config files : /Users/Star/.condarc
                conda version : 4.8.3
          conda-build version : 3.17.8
               python version : 3.7.3.final.0
             virtual packages : __osx=10.15.6
             base environment : /Users/Star/anaconda3  (writable)
                 channel URLs : https://repo.anaconda.com/pkgs/main/osx-64
                                https://repo.anaconda.com/pkgs/main/noarch
                                https://repo.anaconda.com/pkgs/r/osx-64
                                https://repo.anaconda.com/pkgs/r/noarch
                package cache : /Users/Star/anaconda3/pkgs
                                /Users/Star/.conda/pkgs
             envs directories : /Users/Star/anaconda3/envs
                                /Users/Star/.conda/envs
                     platform : osx-64
                   user-agent : conda/4.8.3 requests/2.23.0 CPython/3.7.3 Darwin/19.6.0 OSX/10.15.6
                      UID:GID : 501:20
                   netrc file : None
                 offline mode : False

```python

```
