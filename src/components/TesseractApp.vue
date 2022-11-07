
<template>
    <div>
        <br>
        <v-row justify="space-around">
            <input type="file" name="file" @change="onFileChange" accept="image/png, image/jpg">

            <v-btn depressed color="primary" @click="scan" v-if="image != ''">
                Click for scan
            </v-btn>
        </v-row>
        <br>
        <div class="d-flex flex-column justify-space-between align-center">
            <v-slider v-model="width" class="align-self-stretch" min="300" max="800" step="1"></v-slider>
            <v-img :width="width" :src="image">
            </v-img>
        </div>
        <br>
        <div id="content">
            <v-card>
                <v-responsive>
                    <v-card-text>
                        <v-progress-linear v-model="status" height="25" v-if="status != 0">
                            <strong>{{ Math.ceil(status) }}%</strong>
                        </v-progress-linear>
                        <h3>Scan result :</h3><br>
                        <textarea name="" id="" :style="{ width: '100%', height: '50vh' }">
                        {{ output }}
                        </textarea>

                    </v-card-text>
                </v-responsive>
            </v-card>

        </div>


    </div>

</template>

<script>
import Tesseract from 'tesseract.js';
import { createWorker, PSM, OEM } from 'tesseract.js';
export default {
    data: () => ({
        image: '',
        output: '',
        status: 0,
        width: 400,
    }),
    methods: {
        onFileChange(e) {
            var files = e.target.files || e.dataTransfer.files;
            if (!files.length)
                return;
            this.createImage(files[0]);
        },
        createImage(file) {
            //var image = new Image();
            var reader = new FileReader();

            reader.onload = (e) => {
                this.image = e.target.result;

            };
            reader.readAsDataURL(file);
        },
        scan() {
            const worker = createWorker({
                logger: m => this.status = m.progress * 100
            });
            (async () => {
                await worker.load();
                await worker.loadLanguage('eng+fra');
                await worker.initialize('eng+fra');
                await worker.setParameters({
                    tessedit_ocr_engine_mode: OEM.DEFAULT,
                    tessedit_pageseg_mode: PSM.SINGLE_BLOCK,
                });
                const { data: { text } } = await worker.recognize(this.image);
                console.log(text);
                this.output = text;
                await worker.terminate();
            })();
            /* 
            Tesseract.recognize(
                this.image, 'eng+fra',
                {
                    logger: m => this.status = m.progress * 100
                }
            )
                .catch(err => {
                    console.error(err);
                })
                .then(out => this.output = out.data.text) */
        }
    },
}
</script>