<template>
    <div class="container pt-3">
        <div class="row">
            <div class="col-md-6">
                <div class=" p-2 rounded Form-Container">
                    <p v-if="!data.length" class="text-danger">
                        No data foudn
                    </p>
                    <div v-for="(question, index) of data" :key="index">
                        <div v-if="question.heading" @click="setSelectedQuestion(question)"
                            class="p-2 mb-2 rounded list-item">
                            <h5 class="mb-1">{{ question.heading }}</h5>
                            <p class="mb-1">{{ question && question.questions?.length ? question.questions[0].text : "" }}
                            </p>
                        </div>
                    </div>
                </div>
            </div>

            <div class="col-md-6">
                <div class="">
                    <h3 v-if="!selected._id && !IsSelected">Please select a question to view</h3>
                    <FormComponentPreview v-else hide-live-preview :form-builder="selected" />
                </div>
            </div>

        </div>
    </div>
</template>

<script setup lang="ts">
import { onMounted, reactive, ref } from 'vue';
import axios from 'axios';
import FormComponentPreview from '@/components/FormComponentPreview.vue';

const data = ref<any>([])
const selected = ref({});
const IsSelected = ref(false);

const setSelectedQuestion = (question: object) => {
    IsSelected.value = true;
    selected.value = {};

    // So use can see changes
    setTimeout(() => {
        selected.value = { ...question };
    }, 200);
}

const getQuestions = async () => {
    try {
        const url = `http://127.0.0.1:9100/api/question`;
        const { data: { data: questions } } = await axios.get(url);

        data.value = questions;
    } catch (error) {
        console.log(error);
    }
}

onMounted(() => {
    getQuestions()
})
</script>

<style scoped>
.list-container {
    max-height: calc(90vh - 80px);
    overflow: auto;
}

.list-item {
    background-color: #607d8bb1;
    color: white;
    margin-bottom: 2px;
}

.list-item:hover {
    opacity: 0.8;
    cursor: pointer;
}

.list-item:active {
    opacity: 1;
    cursor: pointer;
}
</style>