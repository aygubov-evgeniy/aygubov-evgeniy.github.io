<template>
	<div class="wrapp">
		<vendor-filter
			:photos="formattedPhotos"
			:likedPhotos="likedPhotos"
			:albums="albumsGroup"
			@changed-like="onChangedLike"
			@changed-remove="onChangedRemove">
		</vendor-filter>
		<div class="wrapp__bg"></div>
	</div>
</template>

<script>
import axios from "axios";
import VendorFilter from "@/components/vendor-filter";

export default {
	name: 'PopupContainer',
	components: {
		VendorFilter
	},

	data() {
		return {
			photos: [],
			formattedPhotos: {},
			likedPhotos: []
		};
	},

	computed: {
		albumsGroup() {
			const albums = {};

			this.likedPhotos.map(item => {
				if(albums[item.albumId]) {
					albums[item.albumId] = [...albums[item.albumId], item]
				}	else {
					albums[item.albumId] = [item]
				}
			})

			return albums
		}
	},

	watch: {
		photos() {
			this.formattedPhotos = this.photos
				.reduce((acc, item) => {
					let firstLetter = item.title.trim().charAt(0).toLowerCase();
					acc[firstLetter] = [...acc[firstLetter] || [], item];
					return acc;
				}, {})

			localStorage.setItem('photos', JSON.stringify(this.formattedPhotos))
		},

		likedPhotos(newValue) {
			localStorage.setItem('likedPhotos', JSON.stringify(newValue))
		}
	},

	mounted() {
		if(localStorage.getItem('likedPhotos')) {
			this.likedPhotos = JSON.parse(localStorage.getItem('likedPhotos'));
		}

		if(localStorage.getItem('photos')) {
			this.formattedPhotos = JSON.parse(localStorage.getItem('photos'));
		}	else {
			axios
				.get('http://jsonplaceholder.typicode.com/photos')
				.then(({ data }) => {
					const counts = {};
					this.photos = data
						.reduce((acc, item) => {
							if(Object.keys(counts).length >= 32) {
								return acc;
							}

							const currentCount = counts[item.albumId] || 0;
							if (currentCount < 10) {
								acc.push(item);
								counts[item.albumId] = currentCount + 1;
							}
							return acc;
						}, [])
						.sort((a, b) => {
							if(a.title < b.title) { return -1; }
							if(a.title > b.title) { return 1; }
							return 0;
						});
				});
		}
	},

	methods: {
		onChangedLike(obj) {
			this.likedPhotos = [...this.likedPhotos, obj]
		},

		onChangedRemove(id, albumId) {
			this.likedPhotos = this.likedPhotos.filter(item => item.id !== id && item.albumId !== albumId)
		}
	}
}
</script>

<style lang="scss">
.wrapp {
	position: relative;
	height: 100vh;
	display: flex;

	&__bg {
		position: fixed;
		top: 0;
		right: 0;
		bottom: 0;
		left: 0;
		z-index: -1;
		background: rgba(27, 32, 79, .2);
	}
}
</style>
