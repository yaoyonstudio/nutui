<template>
    <div class="nut-sidenavbar">
        <div class="sidenavbar-content">
            <div class="sidenavbar-list" ref="list">
                <slot></slot>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    name: "nut-sidenavbar",
    props: {
        offset: {
            type: [String, Number],
            default: 15
        }
    },
    mounted() {
        this.observer = new MutationObserver(
            function(mutations) {
                this.handleSlots();
            }.bind(this)
        );

        this.observer.observe(this.$refs.list, {
            attributes: true,
            childList: true,
            characterData: true,
            subtree: true
        });
    },
    data() {
        return {
            count: 1,
            observer: null
        };
    },
    methods: {
        handleSlots() {
            let slots = this.$slots.default;
            if (slots) {
                slots = slots
                    .filter(item => item.elm.nodeType !== 3)
                    .map(item => {
                        return item.elm;
                    });
                this.setPaddingLeft(slots);
            }
        },
        setPaddingLeft(nodeList, level = 1) {
            for (let i = 0; i < nodeList.length; i++) {
                let item = nodeList[i];
                item.children[0].style.paddingLeft = this.offset * level + "px";
                if (item.className !== "nut-sidenavbaritem") {
                    this.setPaddingLeft(
                        Array.from(item.children[1].children),
                        ++this.count
                    );
                }
            }
            this.count = 1;
        }
    }
};
</script>