<template>
    <div class="Form-Container  rounded p-2 ">
        <div v-if="!hideLivePreview" class="d-flex justify-content-center  pb-3">
            <h2 class="fw-bold ">Live Preview</h2>
        </div>
        <div class="d-flex justify-content-center  pb-3">
            <h3 class="fw-bold">{{ formBuilder.heading }}</h3>
        </div>

        <div class="shadow-sm px-4 py-3 rounded-3 ">
            <template v-for="(question, index) of formBuilder.questions" :key="index">
                <div class="border p-2 rounded mb-2">
                    <h5 class="form-label fw-bold">{{ question.text }}</h5>
                    <div class="row align-items-center g-1">
                        <template v-for="(option, optionIndex) of question.options" :key="optionIndex">
                            <div class="col-md-3">
                                <div class="form-check form-check-inline me-0">
                                    <input v-model="question.selected" class="form-check-input" type="radio"
                                        :name="question.text" :id="'formRadio' + index" :value="option.text">

                                    <label class="form-check-label" :for="'formRadio' + index">{{ option.text }}</label>
                                </div>
                            </div>
                        </template>

                    </div>
                </div>

            </template>
        </div>
    </div>
</template>

<script setup lang="ts">
interface Props {
    formBuilder: object,
    hideLivePreview: boolean,
}
const props = withDefaults(defineProps<Props>(), {
    formBuilder: () => {
        return {
        }
    },
    hideLivePreview: false
})
</script>
