<template>
	<div class="vendor-filter">
		<div class="vendor-filter__header">
			<div class="vendor-filter__title">
				Фильтр:
				<span class="vendor-filter__name" @click="onChangeFilterAlbums">По альбомам</span>
				<span class="vendor-filter__name" @click="onChangeFilterFavorites">Избранное</span>
			</div>
		</div>
		<div class="vendor-filter__scroll-wrap">
			<div class="vendor-filter__scroll">
				<div v-if="sortedByLiked" class="vendor-filter__list">
					<img v-for="({url, title}, index) in likedPhotos" :key="index" :src="url" :alt="title" class="vendor-filter__photo">
				</div>

				<div v-if="sortedByAlbums" class="vendor-filter__list">
					<div v-for="(value, key) in albums" :key="key" class="vendor-filter__part">
						<div class="vendor-filter__part-title vendor-filter__part-title_lg">Альбом {{ key.toUpperCase() }}</div>

						<VendorFilterItem
							v-for="({albumId, id, title, url, thumbnailUrl}, index) in value"
							:key="index"
							:isLiked="checkLiked(id, albumId)"
							v-bind="({albumId, id, title, url, thumbnailUrl})"
							@changed-like="onChangedLike"
							@changed-remove="onChangedRemove">
						</VendorFilterItem>
					</div>
				</div>

				<div v-show="!sortedByLiked && !sortedByAlbums" class="vendor-filter__list">
					<div v-for="(value, key) in photos" class="vendor-filter__part">
						<div class="vendor-filter__part-title">{{ key.toUpperCase() }}</div>

						<VendorFilterItem
							v-for="({albumId, id, title, url, thumbnailUrl}, index) in value"
							:key="index"
							:isLiked="checkLiked(id, albumId)"
							v-bind="({albumId, id, title, url, thumbnailUrl})"
							@changed-like="onChangedLike"
							@changed-remove="onChangedRemove">
						</VendorFilterItem>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import VendorFilterItem from "@/components/vendor-filter-item";

export default {
	name: 'vendor-filter',
	components: {
		VendorFilterItem
	},

	props: {
		photos: {
			type: Object,
			default: () => {}
		},

		likedPhotos: {
			type: Array,
			default: () => []
		},

		albums: {
			type: Object,
			default: () => {}
		}
	},

	data() {
		return {
			sortedByLiked: false,
			sortedByAlbums: false,
			isLiked: false
		};
	},

	methods: {
		onChangedLike(obj) {
			this.$emit('changed-like', obj)
		},

		onChangedRemove(id, albumId) {
			this.$emit('changed-remove', id, albumId)
		},

		onChangeFilterFavorites() {
			this.sortedByLiked = !this.sortedByLiked;
			this.sortedByAlbums = false;
		},

		checkLiked(id, albumId) {
			return this.likedPhotos.some(item => item.id === id && item.albumId === albumId)
		},

		onChangeFilterAlbums() {
			this.sortedByAlbums = !this.sortedByAlbums;
			this.sortedByLiked = false;
		}
	}
}
</script>

<style lang="scss">
.vendor-filter {
	font-weight: 700;
	font-family: Open Sans, Roboto, sans-serif;
	width: 1300px;
	height: 600px;
	margin: auto;
	padding: 3.6rem 4.8rem 6rem;
	font-size: 1.2rem;
	background: #fff;
	border-radius: 1.2rem;
	box-sizing: border-box;

	&__header {
		margin: 0 0 1.5rem;
	}

	&__title {
		font-weight: bold;
		font-size: 1.4rem;
		line-height: 1.9rem;
	}

	&__name {
		margin: 0 .5rem;
		text-decoration: underline;
		cursor: pointer;
	}

	&__scroll-wrap {
		overflow: hidden;
	}

	&__scroll {
		width: 100%;
		max-height: 50rem;
		overflow-y: auto;
	}

	&__list {
		columns: 4;
		position: relative;

		@media (max-width: 991px) {
			columns: 3;
		}

		@media (max-width: 767px) {
			columns: 2;
		}

		@media (max-width: 576px) {
			columns: 1;
		}
	}

	&__part{
		max-width: 260px;
		margin-bottom: 26px;
		height: fit-content;
		display: inline-block;

		@media (max-width: 576px) {
			max-width: 100%;
		}
	}

	&__part-title{
		color: #1b204f;
		font-size: 12px;
		max-width: 52px;
		font-weight: 700;
		text-align: center;
		margin-bottom: 8px;

		&_lg{
			max-width: 100%;
			text-align: left;
		}
	}

	&__photo{
		max-width: 100%;
		margin-bottom: 10px;
	}
}
</style>
