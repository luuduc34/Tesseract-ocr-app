
<template>
    <div>
        <input type="file" @change="onFileChange"><br>
        <div class="d-flex flex-column justify-space-between align-center">
            <v-slider v-model="width" class="align-self-stretch" min="200" max="500" step="1"></v-slider>
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
        width: 300,
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

                Tesseract.recognize(
                    this.image, 'eng',
                    {
                        logger: m => this.status = m.progress * 100
                    }
                )
                    .catch(err => {
                        console.error(err);
                    })
                    .then(
                        ({
                            data: { text }
                        }) => {
                            console.log(text)
                            this.output = text
                        }
                    )
            };
            reader.readAsDataURL(file);
        },
    },
}
</script>