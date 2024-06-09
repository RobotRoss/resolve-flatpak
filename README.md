
resolve-flatpak
===============

This repo allows you to package DaVinci Resolve as a Flatpak for use on Linux Flatpak
based systems, especially e.g. Fedora Silverblue where there aren't easier installation
options. 

It's still a work-in-progress; but "works-for-me[tm]" right now.

Usage
-----

1. Clone this repo with: `git clone -b unified-patches https://github.com/RobotRoss/resolve-flatpak.git --recursive`

2. Learn download ID of the desired version:

## Finding explicit Download IDs (for resolve_download.py)
#### Studio:

```
curl -o- https://www.blackmagicdesign.com/api/support/nz/downloads.json |
    jq -r '.downloads[]
            | select(.urls["Linux"] != null)
            | select(.urls["Linux"][0]["product"] == "davinci-resolve-studio")
            | [.urls["Linux"][0].downloadTitle, .urls["Linux"][0].downloadId]
            | @tsv'
```

#### Free:

```
curl -o- https://www.blackmagicdesign.com/api/support/nz/downloads.json |
    jq -r '.downloads[]
            | select(.urls["Linux"] != null)
            | select(.urls["Linux"][0]["product"] == "davinci-resolve")
            | [.urls["Linux"][0].downloadTitle, .urls["Linux"][0].downloadId]
            | @tsv'
```

3. Overwrite the strings in python/resolve_download.py file. The file is currently configured to download:
DaVinci Resolve 18.6.6

4. 3. Build your package, and export to a distributable single file installer:

#### Free
```
flatpak-builder --force-clean --repo=repo build-dir com.blackmagicdesign.DaVinciResolve.Free.yaml
flatpak build-bundle repo DaVinciResolve.Free.flatpak com.blackmagicdesign.DaVinciResolve.Free
```
#### Studio
```
flatpak-builder --force-clean --repo=repo build-dir com.blackmagicdesign.DaVinciResolve.Studio.yaml
flatpak build-bundle repo DaVinciResolve.Studio.flatpak com.blackmagicdesign.DaVinciResolve.Studio
```

5. Enjoy.

   IF YOU ENCOUNTER ISSUES: If you have appimagelauncherd (the AppImage Launcher daemon) installed and enabled, you NEED to temporarely disable it (either through systemctl or through the AppImage Launcher GUI) as it conflicts with flatpak-builder during the .run file repackaging process.


## Licensing
The icon in logo.png is licensed under the Creative [Commons Attribution-Share Alike 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/deed.en) and fetched from [Wikimedia Commons](https://commons.wikimedia.org/wiki/File:DaVinci_Resolve_Studio.png). It was only cropped afterwards.
