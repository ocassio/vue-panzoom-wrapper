<template>
    <div ref="content">
        <slot/>
    </div>
</template>

<script>
import panzoom from 'panzoom';

export default {
    name: 'PanzoomWrapper',
    props: {
        enabled: {
            type: Boolean,
            required: false,
            default: true
        },
        params: {
            type: Object,
            required: false,
            default: () => {}
        },
        zoomLevel: {
            type: Number,
            required: false,
            default: 1
        }
    },
    data: () => ({
        controller: null
    }),
    mounted() {
        this.init(this.params);
    },
    beforeDestroy() {
        this.destroy();
    },
    watch: {
        enabled(val) {
            if (val) {
                this.controller.resume();
            } else {
                this.controller.pause();
            }
        },
        params(val) {
            this.destroy();
            this.init(val);
        },
        zoomLevel(val) {
            this.zoomTo(val);
        }
    },
    methods: {
        init(params) {
            this.controller = panzoom(this.$refs.content, params);
            this.zoomTo(this.zoomLevel);

            if (!this.enabled) {
                this.controller.pause();
            }
        },
        zoomTo(scale) {
            const { x, y } = this.controller.getTransform();
            this.controller.zoomAbs(x, y, scale);
        },
        destroy() {
            this.controller.dispose();
        }
    }
}
</script>
