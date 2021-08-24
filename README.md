# install-win

## Clenup of uncessary services
run elevated `cmd`
```
sc delete "CCleanerBrowserElevationService"
sc delete "ccleaner"
sc delete "ccleanerm"
```

Windows Image Acquisition (WIA)
Fax
TabletInputService
``` 
sc delete stisvc
sc delete fax
sc delete TabletInputService
```
