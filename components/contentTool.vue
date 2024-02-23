<template>
    <div v-if="toolList.length" class="content-tools">
        <div class="btn-group" role="group" aria-label="content-tools">
            <template v-if="toolList.includes('star')">
                <nuxt-link v-if="$store.state.page.data.starred" :to="doc_action_link($store.state.page.data.document, 'member/unstar')" class="btn btn-secondary tools-btn" v-tooltip="`Unstar`">
                    <span class="fa fa-star"></span>
                    <span class="star-count">{{ $store.state.page.data.star_count }}</span>
                </nuxt-link>
                <nuxt-link v-else-if="$store.state.page.data.star_count || $store.state.page.data.star_count === 0" :to="doc_action_link($store.state.page.data.document, 'member/star')" class="btn btn-secondary tools-btn" v-tooltip="`Star`">
                    <span class="fa fa-star-o"></span>
                    <span class="star-count">{{ $store.state.page.data.star_count }}</span>
                </nuxt-link>
            </template>
            <nuxt-link v-if="toolList.includes('backlink')" :to="doc_action_link($store.state.page.data.document, 'backlink')" class="btn btn-secondary tools-btn">역링크</nuxt-link>
            <nuxt-link v-if="toolList.includes('discuss')" :to="doc_action_link($store.state.page.data.document, 'discuss')" class="btn btn-secondary tools-btn" :class="{ 'btn-discuss-progress': $store.state.page.data.discuss_progress }">토론</nuxt-link>
            <template v-if="toolList.includes('edit')">
                <a v-if="$store.state.page.data.editable === true && $store.state.page.data.edit_acl_message" href="#" @click.prevent="onClickEditBtn" class="btn btn-secondary tools-btn"><span class="fa fa-pencil-square"></span> 편집 요청</a>
                <a v-else-if="$store.state.page.data.editable === false && $store.state.page.data.edit_acl_message" href="#" @click.prevent="onClickEditBtn" class="btn btn-secondary tools-btn"><span class="fa fa-lock"></span> 편집</a>
                <nuxt-link v-else :to="doc_action_link($store.state.page.data.document, 'edit')" class="btn btn-secondary tools-btn"><span class="fa fa-edit"></span> 편집</nuxt-link>
            </template>
            <nuxt-link v-if="toolList.includes('history')" :to="doc_action_link($store.state.page.data.document, 'history')" class="btn btn-secondary tools-btn">역사</nuxt-link>
            <nuxt-link v-if="toolList.includes('acl')" :to="doc_action_link($store.state.page.data.document, 'acl')" class="btn btn-secondary tools-btn">ACL</nuxt-link>
            <nuxt-link v-if="toolList.includes('delete')" :to="doc_action_link($store.state.page.data.document, 'delete')" class="btn btn-danger tools-btn">삭제</nuxt-link>
            <nuxt-link v-if="toolList.includes('move')" :to="doc_action_link($store.state.page.data.document, 'move')"  class="btn btn-secondary tools-btn">이동</nuxt-link>
            <template v-if="toolList.includes('contribution') || toolList.includes('raw') || toolList.includes('blame') || toolList.includes('diff') || toolList.includes('revert') || toolList.includes('menu')">
                <button type="button" class="btn btn-secondary tools-btn dropdown-toggle" data-toggle="dropdown" aria-expanded="false"><span class="caret"></span></button>
                <div class="dropdown-menu dropdown-menu-right" role="menu">
                    <nuxt-link v-if="toolList.includes('contribution')" :to="contribution_author_link($store.state.page.data.document.title)" class="dropdown-item">기여 내역</nuxt-link>
                    <nuxt-link v-if="toolList.includes('raw')" :to="doc_action_link($store.state.page.data.document, 'raw', $store.state.page.data.rev ? { rev: $store.state.page.data.rev } : undefined)" class="dropdown-item">RAW</nuxt-link>
                    <nuxt-link v-if="toolList.includes('blame')" :to="doc_action_link($store.state.page.data.document, 'blame', $store.state.page.data.rev ? { rev: $store.state.page.data.rev } : undefined)" class="dropdown-item">Blame</nuxt-link>
                    <nuxt-link v-if="toolList.includes('diff')" :to="doc_action_link($store.state.page.data.document, 'diff', $store.state.page.data.rev ? { rev: $store.state.page.data.rev, oldrev: $store.state.page.data.rev - 1 } : undefined)" class="dropdown-item">이전 리버전과 비교</nuxt-link>
                    <nuxt-link v-if="toolList.includes('revert')" :to="doc_action_link($store.state.page.data.document, 'revert', $store.state.page.data.rev ? { rev: $store.state.page.data.rev } : undefined)" class="dropdown-item">이 리버전으로 되돌리기</nuxt-link>
                    <nuxt-link v-if="toolList.includes('menu')" v-for="m in $store.state.page.data.menus" :key="m.to" :to="m.to" class="dropdown-item" v-text="m.title" />
                </div>
            </template>
        </div>
    </div>
</template>

<script>
import Common from '~/mixins/common';

export default {
    mixins: [Common],
    methods: {
        onClickEditBtn() {
            if (this.$store.state.localConfig['liberty.showEditMessage']) {
                if (this.requestable)
                    this.$router.push(this.doc_action_link(this.$store.state.page.data.document, 'new_edit_request'));
                else
                    this.$router.push(this.doc_action_link(this.$store.state.page.data.document, 'edit'));
            }
            else {
                this.$store.commit('localConfigSetValue', {key: 'liberty.showEditMessage', value: true});
            }
        }
    },
    watch: {
        $route(to, from) {
            this.$store.commit('localConfigSetValue', {key: 'liberty.showEditMessage', value: false});
        }
    },
    computed: {
        toolList() {
            const tools = [];
            switch (this.$store.state.page.viewName) {
                case 'wiki':
                case 'notfound':
                    tools.push('star', 'backlink', 'discuss', 'edit', 'history', 'acl', 'raw', 'blame', 'diff');
                    if (this.$store.state.page.data.user) tools.push('contribution');
                    if (this.$store.state.page.data.rev) tools.push('revert');
                    break;
                case 'backlink':
                    tools.push('history', 'edit');
                    break;
                case 'edit':
                case 'edit_edit_request':
                    tools.push('backlink', 'delete', 'move');
                    break;
                case 'history':
                    tools.push('edit', 'backlink');
                    break;
                case 'raw':
                case 'diff':
                    tools.push('history', 'edit', 'backlink');
                    break;
                case 'thread':
                    tools.push('discuss');
                    break;
            }
            if (this.$store.state.page.data.menus?.length) tools.push('menu');
            return tools;
        }
    }
}
</script>
