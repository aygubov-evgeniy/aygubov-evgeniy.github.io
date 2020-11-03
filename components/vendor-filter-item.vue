<template>
	<div class="filter-item">
		<img class="filter-item__image" :src="thumbnailUrl" :alt="title" :albumId="albumId">
		<span class="filter-item__title" v-text="title"></span>

		<div v-if="!dataState.isLiked" class="filter-item__icon filter-item__icon_like" @click="onHandleLike()"></div>
		<div v-else class="filter-item__icon filter-item__icon_remove" @click="onHandleRemove()"></div>
	</div>
</template>

<script>
export default {
	name: "vendor-filter-item",
	props: {
		albumId: {
			type: [Number, String],
			default: null
		},

		id: {
			type: [Number, String],
			default: null
		},

		title: {
			type: String,
			default: ''
		},

		url: {
			type: String,
			default: ''
		},

		thumbnailUrl: {
			type: String,
			default: ''
		},

		isLiked: {
			type: Boolean,
			default: false
		},
	},

	data() {
		return {
			dataState: {
				isLiked: this.isLiked
			}
		}
	},

	methods: {
		onHandleLike() {
			this.dataState.isLiked = !this.dataState.isLiked;

			const likedPhoto = {
				id: this.id,
				albumId: this.albumId,
				url: this.url,
				title: this.title,
				thumbnailUrl: this.thumbnailUrl
			}

			this.$emit('changed-like', likedPhoto)
		},

		onHandleRemove() {
			this.dataState.isLiked = !this.dataState.isLiked;
			this.$emit('changed-remove', this.id, this.albumId)
		}
	}
}
</script>

<style scoped lang="scss">
	.filter-item{
		display: flex;
		position: relative;
		border-radius: 8px;
		margin-bottom: 8px;
		background: #F7F8F9;
		align-items: center;
		padding: 4px 35px 4px 4px;

		&:hover{
			& .filter-item__icon{
				opacity: 1;
			}
		}

		&:last-child {
			margin-bottom: 0;
		}

		&__image{
			width: 32px;
			height: 32px;
			border-radius: 8px;
			margin-right: 11px;
		}

		&__title{
			color: #1c214c;
			font-size: 12px;
			font-weight: 700;
		}

		&__icon{
			position: absolute;
			content: '';
			top: 50%;
			right: 7px;
			opacity: 0;
			cursor: pointer;
			transform: translateY(-50%);
			transition: opacity 200ms;

			&_like{
				width: 20px;
				height: 20px;
				background: url('../static/img/heart.svg') no-repeat center/contain;
			}

			&_remove{
				width: 16px;
				height: 16px;
				background: url('../static/img/delete.svg') no-repeat center/contain;
			}
		}
	}
</style>