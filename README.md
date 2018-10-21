# cordova-plugin-zip

A Cordova plugin to unzip files in Android and iOS.

## Installation

    cordova plugin add https://github.com/AGENTxXx/cordova-plugin-zip.git
    
## For ionic

    ionic cordova plugin add https://github.com/AGENTxXx/cordova-plugin-zip.git
    npm install --save @ionic-native/zip

## Usage

    zip.unzip(<source zip>, <destination dir>, <callback>, [<progressCallback>]);

Both source and destination arguments can be URLs obtained from the HTML File
interface or absolute paths to files on the device.

The callback argument will be executed when the unzip is complete, or when an
error occurs. It will be called with a single argument, which will be 0 on
success, or -1 on failure.

The progressCallback argument is optional and will be executed whenever a new ZipEntry
has been extracted. E.g.:

    var progressCallback = function(progressEvent) {
        $( "#progressbar" ).progressbar("value", Math.round((progressEvent.loaded / progressEvent.total) * 100));
    };

The values `loaded` and `total` are the number of compressed bytes processed and total. Total is the
file size of the zip file.

Example for Ionic 3:

build.gradle:

    android {
        ...
        aaptOptions {
            noCompress "zip" //if you want correct total file size in progress callback
        }
        ...
    }

home.html:

    <ion-content class="menuBg" scrollbar-x="false" scrollbar-y="false">
        <div id="progressbarPosition" class="progressbarPosition" [style.width]="progressbarPercent"></div>
    </ion-content>
    
home.ts:
    
    import { NavController, Platform} from 'ionic-angular';
    import { Component, ChangeDetectorRef } from '@angular/core';
    import { Zip } from '@ionic-native/zip';
    
    public progressbarPercent:string = '0%';
    public progress = 0;

    constructor(public pl: Platform, private file: File, private zip: Zip, private cdr: ChangeDetectorRef) {
        platform.ready().then((readySource) => {
        this.zip.unzip(this.file.applicationDirectory +'www/assets/map.zip', this.file.externalDataDirectory, 
                (progress) => {
                    let pr = Math.round((progress.loaded / progress.total) * 100);
                    if (this.progress != pr) {
                        this.progress = pr;
                        this.progressbarPercent = this.progress + "%";
                        this.cdr.detectChanges(); //is update view manual, another is not working auto-update
                    }
                }
            )
            .then((result) => {
                if(result === 0) {
                    //It's OK
                }
                else {
                    //It's Error
                };
            });
        }
    }

## Release Notes

### 3.1.1 (Oct 21, 2018)
* Fixed an error when the file could not be found.
* Corrected the error when the file size was calculated incorrectly.

### 3.1.0 (Feb 23, 2016)
* Updated SSZipArchive (ios lib) to 1.1

### 3.0.0 (May 1, 2015)
* Updated SSZipArchive (ios lib) to 0.2.1
* Update file plugin dependency to use npm version (cordova-plugin-file)

### 2.1.0 (May 1, 2014)
* Added progress events
* Fix iOS path handling when given file: URLs as src/target
