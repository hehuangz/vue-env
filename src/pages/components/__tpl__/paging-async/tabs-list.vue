<template>
	<i-tabs 
		:value="type" 
		:animated="false"
		type="card"
		@on-click="handleChange"
	>
		<i-tab-pane 
			v-for="(item) in tabs"
			:key="item.value"
			:label="item.label" 
			:name="item.value"
		>
			<vc-paging
				:show="item.value == type" 
				:type="item.value"
				:columns="columns" 
				:data-source="listInfo[item.value].data"
				:total="listInfo[item.value].total"
				:reset="listInfo[item.value].reset"
				:current.sync="current[item.value]"
				:history="true"
				:load-data="loadData"
				@page-size-change="handleChangePageSize"
			/>
		</i-tab-pane>
	</i-tabs>
</template>

<script>
import { Tabs, TabPane, Input } from 'iview';
import { Paging } from 'wya-vc';
import { getParseUrl, getHashUrl } from '@utils/utils';
import { setTimeout } from 'timers';
// item
import item from './item';

export default {
	name: 'tpl-paging-tabs-list',
	components: {
		'vc-paging': Paging,
		'i-tabs': Tabs,
		'i-tab-pane': TabPane,
		'i-input': Input
	},
	mixins: [item],
	data() {
		const { query } = this.$route;

		return {
			type: String(query.type || "1"), // 同tabs下的value
			current: {}
		};
	},
	computed: {
		tabs() {
			return this.$store.state.tplPagingAsync.tabs;
		},
		listInfo() {
			return this.$store.state.tplPagingAsync.listInfo;
		}
	},
	created() {
		setTimeout(() => {
			this.request({
				url: 'TPL_PAGING_ASYNC_TABS_GET',
				type: 'GET',
				localData: {
					data: [],
					status: 1,
					msg: 'success'
				}
			}).then((res) => {
				console.log(res, 'res');
			}).catch((error) => {
				console.log(error, 'error');
			});
		}, 300);
	},
	methods: {
		loadData(page, pageSize) {
			const { query = {} } = getParseUrl();
			return this.request({
				url: 'TPL_PAGING_ASYNC_LIST_GET',
				type: 'GET',
				param: {
					...query,
					page,
					pageSize,
					type: this.type
				},
			}).then((res) => {
				console.log(res, 'res');
			}).catch((error) => {
				console.log(error, 'error');
			});
		},
		handleChange(type) {
			this.type = type;

			let { query = {} } = getParseUrl();
			query = {
				...query,
				type,
				page: this.current[type]
			};
			this.$router.replace(getHashUrl(`/tpl/paging/async`, { ...query }));
		},
		handleChangePageSize() {
			this.$store.commit('TPL_PAGING_ASYNC_LIST_INIT');
		},
	}
};
</script>

<style lang="scss" scoped>

</style>
