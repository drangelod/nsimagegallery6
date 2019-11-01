<template>
    <Page class="page">
        <ActionBar class="action-bar">
            <Label class="action-bar-title" text="Picture Gallery"></Label>
        </ActionBar>
        <StackLayout>
            <Label text.decode="&#xf030; " @tap="takePicture" class="take-picture-icon fa" />
            <ScrollView class="picture-gallery" orientation="vertical">
                <StackLayout orientation="vertical">
                    <GridLayout v-for="image in arrayPictures" :key="image.id" columns="*" rows="*">
                        <Image class="gallery-item" col="0" row="0" :src="image" stretch="aspectFill" @tap="tapPicture(image)" />
                        <StackLayout v-if="image.note.length>0" col="0" row="0" class="note-picture-wrapper" @tap="tapPicture(image)">
                            <Label textWrap="true" verticalAlignement="bottom" :text="image.note" class="note-picture-text"></Label>
                        </StackLayout>
                    </GridLayout>
                </StackLayout>
            </ScrollView>
        </StackLayout>
    </Page>
</template>

<script>
const applicationSettings = require("application-settings");
const fsModule = require("tns-core-modules/file-system");
const imageSourceModule = require("tns-core-modules/image-source");
const cameraModule = require("nativescript-camera");
import ImageDetails from "@/components/ImageDetails";
export default {
    computed: {},
    data() {
        return {
            arrayPictures: []
        };
    },
    methods: {
        tapPicture(image) {
            let navContextObj = {
                image: image,
                arrayPictures: this.arrayPictures,
                storeData: this.storeData
            };
            this.$navigateTo(ImageDetails, {
                animated: true,
                transition: {
                    name: "slideLeft",
                    curve: "easeInOut",
                    duration: 100
                },
                props: { navObject: navContextObj }
            });
        },
        takePicture() {
            let that = this;
            cameraModule
                .takePicture({
                    width: 300, //these are in device independent pixels
                    height: 300, //only one will be respected depending on os/device if
                    keepAspectRatio: true, //    keepAspectRatio is enabled.
                    saveToGallery: false //Don't save a copy in local gallery, ignored by some Android devices
                })
                .then(imageAsset => {
                    imageSourceModule.fromAsset(imageAsset).then(
                        savedImage => {
                            let filename = "image" + "-" + new Date().getTime() + ".png";
                            let folder = fsModule.knownFolders.documents();
                            let path = fsModule.path.join(folder.path, filename);
                            savedImage.saveToFile(path, "png");
                            var loadedImage = imageSourceModule.fromFile(path);
                            loadedImage.filename = filename;
                            loadedImage.note = "";
                            that.arrayPictures.unshift(loadedImage);
                            that.storeData();
                        },
                        err => {
                            console.log("Failed to load from asset");
                        }
                    );
                })
        },
        storeData() {
            let localArr = [];
            this.arrayPictures.forEach(entry => {
                localArr.push({ note: entry.note, filename: entry.filename });
            })
            applicationSettings.setString("localdata", JSON.stringify(localArr));
        },
        loadData() {
            let strData = applicationSettings.getString("localdata");
            if (strData && strData.length) {
                let localArr = JSON.parse(strData);
                localArr.forEach(entry => {
                    const folder = fsModule.knownFolders.documents();
                    const path = fsModule.path.join(folder.path, entry.filename);
                    var loadedImage = imageSourceModule.fromFile(path);
                    loadedImage.filename = entry.filename;
                    loadedImage.note = entry.note;
                    this.arrayPictures.unshift(loadedImage);
                })
            }
        },
    },
    mounted() {
        cameraModule.requestPermissions().then( //request permissions for camera
            success => { //have permissions  
            },
            failure => { //no permissions for camera
            }
        )
        this.loadData()
    }
}
</script>

<style scoped lang="scss">
.take-picture-icon {
    horizontal-align: center;
    background-color: rgb(105, 105, 241);
    padding: 12;
    border-width: 1.2;
    border-color: black;
    border-radius: 14;
    margin-top: 20;
    margin-bottom: 20;
    color: white;
    font-size: 30;
    padding-left: 20;
}

.picture-gallery {
    margin-top: 20;
}

.gallery-item {
    margin: 10;
}

.note-picture-wrapper {
    background-color: #1a1919;
    opacity: 0.7;
    border-width: 1;
    border-radius: 8;
    color: #ffffff;
    border-color: #ffffff;
    margin: 15;
    vertical-align: bottom;
    horizontal-align: center;
}

.note-picture-text {
    font-size: 15;
    vertical-align: center;
    horizontal-align: center;
    padding: 4;
}
</style>