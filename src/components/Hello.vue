<template>
    <div>
        <div class="greeting">Hello {{name}}{{exclamationMarks}}</div>
        <button @click="decrement">-</button>
        <button @click="increment">+</button>
        <color-picker :initialColor="{r:123, g: 24, b: 21}" />
    </div>
</template>

<script lang="ts">
import Vue from "vue";
import { Component, Emit, Inject, Model, Prop, Provide, Watch } from 'vue-property-decorator'
import ColorPicker from "./ColorPicker.vue";


@Component({components: {ColorPicker}})
export default class HelloDecorator extends Vue {
    @Prop() name: string;
    @Prop() initialEnthusiasm: number;
    enthusiasm = this.initialEnthusiasm;
    increment() {
        this.enthusiasm++;
    }
    decrement() {
        if (this.enthusiasm > 1) {
            this.enthusiasm--;
        }
    }
    get exclamationMarks(): string {
        return Array(this.enthusiasm + 1).join('!');
    }
}
</script>

<style>
.greeting {
    font-size: 20px;
}
</style>