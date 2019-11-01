<template>
    <Page class="page" ref="page" actionBarHidden="false" backgroundSpanUnderStatusBar="true">
    
        <ActionBar class="action-bar" title="Picture Details">
            <NavigationButton text="Done" android.systemIcon="ic_menu_back" @tap="$navigateBack()" />
            <Label class="action-bar-title" text="Picture Details"></Label>
        </ActionBar>
        <ScrollView orientation="vertical">
            <StackLayout>
                <Image class="picture-full" stretch="aspectFit" :src="navObject.image" />
                <GridLayout columns="*,*" rows="60,30,*,300">
                    <StackLayout col="1" row="0" class="delete-picture-icon-wrapper" @tap="deletePicture">
                        <Label verticalAlignement="bottom" text="delete" class="delete-picture-icon"></Label>
                    </StackLayout>
                    <Label col="0" colSpan="2" row="1" text="Note:" class="section-label" />
                    <TextView col="0" colSpan="2" row="2" class="text-picture" hint="Add a note for this picture here" editable="true" v-model="navObject.image.note" />
                    <Label col="0" colSpan="2" row="3" text="" />
                </GridLayout>
            </StackLayout>
        </ScrollView>
    </Page>
</template>

<script>
export default {
    name: "image-details-page",
    data() {
        return {};
    },
    props: {
        navObject: {
            type: Object
        },
    },
    components: {},
    computed: {},
    created() {},
    beforeDestroy() {
        this.navObject.storeData()
    },
    mounted() {},
    methods: {
        deletePicture() {
            let pictureIndex = this.navObject.arrayPictures.indexOf(this.navObject.image);
            this.navObject.arrayPictures.splice(pictureIndex, 1);
            this.$navigateBack()
        }
    }
};
</script>

<style scoped>
.delete-picture-icon {
    font-size: 15;
    vertical-align: center;
    horizontal-align: center;
}

.delete-picture-icon-wrapper {
    background-color: #000000;
    border-width: 1;
    border-radius: 8;
    color: #ffffff;
    border-color: #ffffff;
    margin: 15;
    vertical-align: center;
    horizontal-align: right;
    height: 30;
    width: 60;
}

.text-picture {
    border-width: 1;
    border-style: solid;
    border-color: #01060c;
    height: 80;
    background-color: rgb(235, 233, 233);
}

.section-label {
    background-color: #292b2b;
    border-width: 1;
    border-style: solid;
    border-color: #01060c;
    color: white;
    padding-left: 10;
    padding-top: 5;
    padding-bottom: 5;
}

.picture-full {
    border-width: 1;
    border-color: gray;
}
</style>
