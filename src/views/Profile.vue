<template>
    <v-container>
        <v-card>
            <v-card-title>
                Create a Modpack Profile
            </v-card-title>
            <v-card-text>
                <v-form v-model="valid">
                    <v-container>
                        <v-row>
                            <v-col cols="4" md="4">
                                <v-text-field v-model="firstname" :rules="nameRules" :counter="10" :label="'https://letsplay.one/' + store.currentUser.username"
                                    required hide-details></v-text-field>
                            </v-col>
                        </v-row>
                    </v-container>
                    <v-file-input label="Upload mods configuration" @change="handleZipFile"></v-file-input>
                </v-form>
                <v-data-table v-if="Mods.length > 0" :items="Mods"></v-data-table>
            </v-card-text>
            <v-card-actions class="float-right">
                <v-btn color="success" @click="save" >Save</v-btn>
                <v-btn color="red" @click="delete">Delete</v-btn>
            </v-card-actions>
        </v-card>
    </v-container>
    <!-- <v-app-bar color="surface-variant" title="ModGame"></v-app-bar> -->

    <v-form v-model="valid">
        <v-container>
            <v-row>
                <v-col cols="4" md="4">
                    <v-text-field v-model="firstname" :rules="nameRules" :counter="10" label="First name" required
                        hide-details></v-text-field>
                </v-col>

                <v-col cols="4" md="4">
                    <v-text-field v-model="lastname" :rules="nameRules" :counter="10" label="Last name" hide-details
                        required></v-text-field>
                </v-col>

                <v-col cols="4" md="4">
                    <v-text-field v-model="email" :rules="emailRules" label="E-mail" hide-details required></v-text-field>
                </v-col>
            </v-row>
        </v-container>
        <v-file-input label="Upload mods configuration" @change="handleZipFile"></v-file-input>



        <v-textarea label="Custom Css"></v-textarea>
        <v-card class="preview-box">
        </v-card>



        <v-btn color="primary">Preview</v-btn>


        <!-- Your content goes here -->
    </v-form>
</template>

<script setup>
import JSZip from 'jszip';
import { ref } from 'vue';
import { appStore } from '@/store/app';

const store = appStore();
if (store.pb.authStore.isValid) {
    store.currentUser = store.pb.authStore.model;
}
const Mods = ref([]);

function handleZipFile(event) {
    const file = event.target.files[0];
    // Verify file extension
    if (!file.name.endsWith('.r2z')) {
        console.error('Invalid file type. Please upload a zip file.');
        return;
    }
    JSZip.loadAsync(file).then((zip) => {
        // Iterate through each file in the zip
        Object.keys(zip.files).forEach((filename) => {
            zip.files[filename].async('string').then((fileContent) => {
                // Only Process the mods.yml and manifest.json files
                if (filename === 'mods.yml') {
                    // Process the mods.yml file
                    Mods.value = (yamlToJson(fileContent));
                }
            });
        });
    }).catch((err) => {
        console.error('Error reading zip file:', err);
    });
}

function yamlToJson(yaml) {
    const result = [];
    let index = -1; // Fixes issue with manifestVersion not being in the first index
    const lines = yaml.split('\n');
    for(var i = 0; i < lines.length; i++) {
        var line = lines[i].trim();
        if(line && line.charAt(0) !== '#') { // skip empty lines and comments
            // If it is a manifestVersion line, increment the index and set the value of result[index] to an empty object
            if(line.indexOf('- manifestVersion') > -1) {
                index++;
                result[index] = {};
                continue;
            }
            var colonIndex = line.indexOf(':');
            if(colonIndex > -1) {
                var key = line.substr(0, colonIndex).trim();
                var value = line.substr(colonIndex + 1).trim();
                // Basic attempt to interpret arrays (very simplistic)
                if (value.charAt(0) === '[' && value.charAt(value.length - 1) === ']') {
                    value = value.substring(1, value.length - 1).split(',').map(item => item.trim());
                }
                result[index][key] = value;
            }
        }
    }
    return result;
}
</script>




<!-- ADD Functionality for preview -->

<style scoped>
.v-application {
    background-color: #f5f5f5;
}

.preview-box {
    width: 300px;
    height: 300px;
    background-color: #f5f5f5;
    border: 1px solid #ccc;
    /* Add any other styles you want */
}
</style>





# add a function to preview the custom css
function preview() {
    // get the custom css
    // apply the custom css to the preview box
}


# add a function to upload the mods configuration
# add a function to save the profile
# add a function to load the profile
# add a function to delete the profile
# add a function to reset the profile