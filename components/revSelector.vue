<template>
    <div v-if="rev && ($store.state.page.viewName === 'diff' || $store.state.page.viewName === 'blame')">
        <ul class="pagination pagination-sm">
            <li class="page-item" :class="{ disabled: currentPage === 0 }">
                <a class="page-link" href="#" @click.prevent="prevPage"><span class="ion-ios-arrow-back"></span> Prev</a>
            </li>
            <li v-for="n in itemList" :key="n" class="page-item">
                <nuxt-link v-if="$store.state.page.viewName === 'diff'" :to="doc_action_link($store.state.page.data.document, 'diff', { rev, oldrev: n })" class="page-link">{{ n }}</nuxt-link>
                <nuxt-link v-if="$store.state.page.viewName === 'blame'" :to="doc_action_link($store.state.page.data.document, 'blame', { rev: n })" class="page-link">{{ n }}</nuxt-link>
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
            if (this.currentPage > 0) {
                this.currentPage--;
            }
        },
        nextPage() {
            if (this.currentPage < this.pageCount) {
                this.currentPage++;
            }
        }
    },
    watch: {
        $route() {
            this.currentPage = 0;
        }
    },
    computed: {
        itemLength() {
            return window.innerWidth < 610 ? 5 : 10;
        },
        rev() {
            return this.$store.state.page.data.rev || this.$route.query.rev;
        },
        pageCount() {
            return Math.ceil((this.rev - 1) / this.itemLength) - 1;
        },
        itemList() {
            const items = [];
            for (let i = this.rev - 1 - this.currentPage * this.itemLength; i > 0 && items.length < this.itemLength; i--) {
                items.push(i);
            }
            return items;
        }
    },
};
</script>