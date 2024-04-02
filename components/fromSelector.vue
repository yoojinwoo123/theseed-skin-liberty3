<template>
    <div v-if="$store.state.localConfig['liberty.rev_selector'] !== false && $store.state.page.viewName === 'history'">
        <input v-model="revInput" type="text" inputmode="numeric" pattern="\d*" class="form-control" placeholder="rev">
        <button @click="onClickSearch" type="button" class="btn btn-secondary">
            <span class="fa fa-search"></span>
        </button>
    </div>
</template>

<style scoped>
div {
    float: right;
    margin: 1rem;
}

input {
    width: 5rem;
    display: inline-block;
}

button > .fa {
    font-size: 0.9rem;
}

.theseed-dark-mode input,
.theseed-dark-mode button {
    background-color: #27292d;
    border-color: #383b40;
    color: #ddd;
}

.theseed-dark-mode button:hover {
    background-color: #2d2f34;
    border-color: #383b40;
}
</style>

<script>
import Common from '~/mixins/common';

export default {
    mixins: [Common],
    data() {
        return {
            revInput: this.$route.query.from ?? ''
        }
    },
    methods: {
        onClickSearch() {
            if (!this.revInput) return;
            this.$router.push(this.doc_action_link(this.$store.state.page.data.document, 'history', { from: this.revInput }));
        }
    },
    watch: {
        '$route.query.from'() {
            this.revInput = this.$route.query.from;
        }
    }
}
</script>