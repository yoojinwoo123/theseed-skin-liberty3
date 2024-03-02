<template>
    <div v-if="($store.state.page.data.rev || $route.query.rev) && ($store.state.page.viewName === 'diff' || $store.state.page.viewName === 'blame')">
        <ul class="pagination pagination-sm">
            <li class="page-item" :class="{ disabled: currentPage === 0 }">
                <a class="page-link" href="#" @click.prevent="prevPage"><span class="ion-ios-arrow-back"></span> Prev</a>
            </li>
            <li v-for="n in count" :key="rev - n - currentPage * 10" class="page-item">
                <nuxt-link :to="generateItemLink(n)" class="page-link">{{ rev - n - currentPage * 10 }}</nuxt-link>
            </li>
            <li class="page-item" :class="{ disabled: currentPage === pageCount }">
                <a class="page-link" href="#" @click.prevent="nextPage">Next <span class="ion-ios-arrow-forward"></span></a>
            </li>
        </ul>
    </div>
</template>

<style scoped>
.pagination {
    margin-top: 0;
    justify-content: center;
    display: flex;
}

.theseed-dark-mode .pagination .page-item .page-link {
    background-color: #27292d;
    border-color: #383b40;
}
</style>

<script>
import Common from '~/mixins/common';

export default {
    mixins: [Common],
    data() {
        return {
            currentPage: 0,
        }
    },
    methods: {
        prevPage() {
            if (this.currentPage > 1) {
                this.currentPage--;
            }
        },
        nextPage() {
            if (this.currentPage < this.pageCount) {
                this.currentPage++;
            }
        },
        generateItemLink(n) {
            if (this.$store.state.page.viewName === 'diff') {
                return this.doc_action_link(this.$store.state.page.data.document, 'diff', { rev: this.rev, oldrev: this.rev - n - this.currentPage * 10 })
            }
            else if (this.$store.state.page.viewName === 'blame') {
                return this.doc_action_link(this.$store.state.page.data.document, 'blame', { rev: this.rev - n - this.currentPage * 10 })
            }
        }
    },
    computed: {
        rev() {
            return this.$store.state.page.data.rev || this.$route.query.rev;
        },
        pageCount() {
            return Math.floor(this.rev / 10);
        },
        count() {
            return this.currentPage === this.pageCount ? this.rev % 10 - 1 : 10;
        }
    },
};
</script>