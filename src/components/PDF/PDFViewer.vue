<template>
    <div class='pdf-viewer'>
        <header class='pdf-header'>
            <button @click="slideChange(-1)">
                Previous 
            </button>
            <span>
                Page <input disabled class='input-box' input='number' v-model="currentSlideNum" /> of <b>{{ numPages }}</b>
            </span>
            <button @click="slideChange(1)">
                Next
            </button>
        </header>
        <PDFData>
            <PDFDocument slot='document' slot-scope="{pages}" :pages='pages' />
        </PDFData>
        <footer class='zoom-button'>
            <button @click="zoom(-.25)">-</button>
            <button @click="zoom(.25)">+</button>
        </footer>
    </div>
</template>

<script>
import { mapState, mapGetters } from 'vuex';
import PDFData from './PDFData.vue';
import PDFDocument from './PDFDocument.vue';

import { CHANGE_SLIDE } from '../../store/actions.type';

export default {
    name: 'pdf-viewer',
    computed: {
        ...mapState({
            showPdf: state => state.SlideModule.slideStatus == 2,
            numPages: state => state.SlideModule.numPages,
            currentSlideNum: state => state.SlideModule.currentSlideIndex + 1
        }),
        ...mapGetters({
            pages: 'loadedPages'
        })
    },
    components: {
        PDFData,
        PDFDocument
    },
    methods: {
        /**
         * Dispatch the slide change action
         */
        slideChange(changeVal) {
            this.$store.dispatch(CHANGE_SLIDE, changeVal);
        },
        keyChange(ev) {
            let changeVal;
            switch (ev.key) {
                case 'ArrowRight':
                case 'ArrowDown':
                    changeVal = 1
                    break;
                case 'ArrowLeft':
                case 'ArrowUp':
                    changeVal = -1;
                    break;
                default:
                    break;
            }

            if (changeVal) {
                this.slideChange(changeVal)                
            }
        },
        zoom(changeVal) {
            console.log(changeVal)
            this.$root.$emit('zoom-change', changeVal);
        }
    },
    created() {
        window.addEventListener('keydown', this.keyChange);
    },
    beforeDestroy() {
        window.removeEventListener('keydown', this.keyChange)
    },
}
</script>

<style>
.pdf-viewer {
    width: 100%;
    display: flex;
    flex-direction: column;
    justify-content: flex-end;
}

.pdf-header {
    display: flex;
    position: relative;
    padding: .5rem 0;
    justify-content: space-around;
    background-color:rgba(0, 0, 0, .5);
    text-align: center;
    color: #FFF;
    width: 100%;
}

.input-box {
    font-weight: 600;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    width: 25px;
    text-align: center;
}

.zoom-button {
    position: absolute;
    bottom: 25px;
    right: 25px;
    user-select: none;
}

</style>
