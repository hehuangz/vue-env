<template>
	<vc-paging
		ref="tableTarget"
		:data-source="listInfo.data"
		:total="listInfo.total" 
		:reset="listInfo.reset"
		:history="true"
		:load-data="loadData"
		mode="piece"
		class="g-m-t-20 v-tpl-paging-piece"
		@page-size-change="handleChangePageSize"
	>
		<tpl-item 
			slot-scope="it"
			v-bind="it"
			class="_item"
		/> 
	</vc-paging>
</template>

<script>
import { Paging } from 'wya-vc';
import { getParseUrl } from '@utils/utils';
// item
import Item from './item';

export default {
	name: 'tpl-table1',
	components: {
		'vc-paging': Paging,
		'tpl-item': Item,
	},
	data() {
		return {};
	},
	computed: {
		listInfo() {
			return this.$store.state.tplPagingPiece.listInfo;
		}
	},
	methods: {
		loadData(page, pageSize) {
			const { query = {} } = getParseUrl();
			return this.request({
				url: 'TPL_PAGING_PIECE_LIST_GET',
				type: 'GET',
				param: {
					...query,
					page,
					pageSize,
				},
			}).then((res) => {
				console.log(res, 'res');
			}).catch((error) => {
				console.log(error, 'error');
			});
		},
		handleChangePageSize() {
			this.$store.commit('TPL_PAGING_PIECE_LIST_INIT');
		}
	}
};

</script>

<style lang="scss">
.v-tpl-paging-piece {
	display: flex;
	flex-wrap: wrap;
	justify-content: space-between;
	margin: 20px 0;
	._item {
		border: 1px solid #d4d4d4;
		padding: 20px;
		margin-bottom: 20px;
		width: calc(50% - 10px);
	}
}
</style>

