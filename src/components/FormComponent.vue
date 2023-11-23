<template>
    <div class="row">
        <div class="col-md-6">
            <div class="Form-Container  rounded p-2 ">
                <form @submit.prevent="submitForm">
                    <div class="d-flex justify-content-center pb-3">
                        <input type="text" class="form-control w-50" name="heading" required placeholder="Enter Heading"
                            v-model="formBuilder.heading">
                    </div>
                    <div class="shadow-sm px-4 py-3 rounded-3 ">
                        <template v-for="(question, index) of formBuilder.questions" :key="index">
                            <div class="border p-2 rounded mb-2">
                                <label class="form-label">Enter question # {{ index + 1 }}</label>
                                <input v-model="question.text" :name="'question' + index" :id="'question' + index"
                                    type="text" class="form-control mb-2" placeholder="Enter Question" required>
                                <div class="row align-items-center g-1">
                                    <template v-for="(option, optionIndex) of question.options" :key="optionIndex">
                                        <div class="col-md-3">
                                            <div class="form-check form-check-inline me-0">
                                                <input disabled class="form-check-input" type="radio" :name="question.text"
                                                    :id="'formRadio' + index" :value="option.text">

                                                <input v-model="option.text" class="form-control form-control-sm "
                                                    :for="'formRadio' + index" :name="'formRadio' + index"
                                                    placeholder="Question Text" required>
                                            </div>
                                        </div>
                                    </template>
                                    <div class="col-md-12 ">
                                        <div class="d-flex gap-2 justify-content-end">
                                            <button v-if="question.options.length" type="button"
                                                @click="remove('option', index)" class="btn-sm  btn btn-outline-danger">
                                                Remove Option
                                            </button>
                                            <button @click="addOption(question.options)" type="button"
                                                class="btn btn-sm btn-outline-danger ">
                                                Add Option
                                            </button>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="text-end mb-2 ">
                                <button type="button" v-if="index != 0" @click="remove('question', index)"
                                    class="btn-sm btn btn-outline-danger">
                                    Remove Question
                                </button>
                                <button type="button" v-if="index == formBuilder.questions.length - 1" @click="addQuestion"
                                    class="btn-sm btn btn-outline-danger ms-2">
                                    Add Question
                                </button>
                            </div>
                        </template>
                    </div>

                    <div class="mt-3 text-center ">
                        <button :disabled="processing" type="submit" class="btn btn-danger px-4">
                            SUBMIT
                        </button>
                    </div>
                </form>
            </div>

        </div>

        <div class="col-md-6">
            <FormComponentPreview :form-builder="formBuilder" />
        </div>

        <div class="toast-container position-fixed bottom-0 end-0 p-3 ">
            <div id="liveToast" class="toast " :class="[toast.type === 'error' ? 'bg-danger' : 'bg-success']" role="alert"
                aria-live="assertive" aria-atomic="true">
                <div class="toast-header">
                    <strong class="me-auto">{{ toast.type }}</strong>
                    <small>{{ new Date().toLocaleString() }}</small>
                    <button type="button" class="btn-close" data-bs-dismiss="toast" aria-label="Close"></button>
                </div>
                <div class="toast-body text-white">
                    {{ toast.message }}
                </div>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">

import * as bootstrap from 'https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.esm.min.js'
import FormComponentPreview from '@/components/FormComponentPreview.vue';
import { onMounted, reactive, nextTick, ref } from 'vue';
import axios from 'axios'
import process from 'process';

type Toast = {
    type: string,
    message: string
}

type Option = {
    text: "",
}
let toastEl = null
let toastInstance: object = {};


const questionPrototype = {
    text: "",
    isMandatory: false,
    answer: "",
    options: [{
        text: "",
    }]
};

// The reactive objects

const formBuilder = reactive({
    heading: "",
    questions: [JSON.parse(JSON.stringify(questionPrototype))]

})

const processing = ref(false);

const toast: Toast = reactive({
    type: 'success', // error or success
    message: "",
})


// Methods

function addOption(options: Array<object>): void {
    options.push({
        text: "",
    })
}

function addQuestion(): void {


    const lastIndex = formBuilder.questions.length - 1;
    const isAtleastThree = formBuilder.questions[lastIndex].options.length > 2;
    if (!isAtleastThree) {
        // const toastEl = document.getElementById('liveToast')
        // const toastInstance = bootstrap.Toast.getOrCreateInstance(toastEl)
        toast.type = 'error';
        toast.message = "Please add atleast 3 options for question " + (+lastIndex + 1);
        showToast();
        return;
    }

    formBuilder.questions.push(JSON.parse(JSON.stringify(questionPrototype)))
}


function remove(type: string, index: number): void {
    if (type == 'question') {

        formBuilder.questions.splice(index, 1);
        return
    }

    if (type === 'option') {
        formBuilder.questions[index].options.pop();
    }

}

async function submitForm(): Promise<void> {
    try {
        processing.value = true;
        const isDuplicate = formBuilder.questions.some((q) => q.options.some((op1: Option, index: number) => {
            const nextOption = q.options[index + 1];
            if (nextOption)
                return nextOption.text == op1.text
            else {
                return false
            }
        }));

        if (isDuplicate) {
            toast.type = 'error';
            toast.message = "Rola Hogya Boss. Options should not be same";
            toastInstance.show();
            return;
        }

        const indexFound = formBuilder.questions.findIndex(q => q.options.length < 3);

        if (indexFound > -1) {
            toast.type = 'error';
            toast.message = "Oho, " + (+indexFound + 1) + " should have atleast three questions";
            toastInstance.show();
            return;
        }
        const url: string = `http://127.0.0.1:9100/api/question/create`
        const response = await axios.post(url, formBuilder);

        toast.type = 'success';
        toast.message = "Question saved successfully";

        showToast();

        formBuilder.heading = "";
        formBuilder.questions = [JSON.parse(JSON.stringify(questionPrototype))];

    } catch (error) {
        console.log(error);
    } finally {
        processing.value = false;
    }
}

onMounted(() => {
    toastEl = document.getElementById('liveToast');
    toastInstance = bootstrap.Toast.getOrCreateInstance(toastEl);
})

function showToast() {
    nextTick(() => {
        toastInstance.show()
    })
}

</script>

 