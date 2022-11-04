
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
                        <p>{{ output }}</p>
                    </v-card-text>
                </v-responsive>
            </v-card>

        </div>
    </div>

</template>

<script>
import Tesseract from 'tesseract.js';

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

                /* Tesseract.recognize(
                    this.image, 'eng+fra',
                    {
                        logger: m => this.status = m.progress * 100
                    }
                )
                    .catch(err => {
                        console.error(err);
                    })
                    .then(out => this.output = out.data.text) */
                /*  .then(
                     ({
                         data: { text }
                     }) => {
                         console.log(text)
                         this.output = text
                     }
                 ) */
            };
            reader.readAsDataURL(file);
        },
        scan() {
            Tesseract.recognize(
                this.image, 'eng+fra',
                {
                    logger: m => this.status = m.progress * 100
                }
            )
                .catch(err => {
                    console.error(err);
                })
                .then(out => this.output = out.data.text)
        }
    },
}
</script>