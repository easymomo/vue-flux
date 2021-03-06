<template>
	<div :style="style">
		<flux-cube
			v-for="i in numTiles"
			:key="i"
			:slider="slider"
			:index="index"
			:css="getTileCss(i)"
			ref="tiles">
		</flux-cube>
	</div>
</template>

<script>
	import FluxCube from './FluxCube.vue';

	export default {
		name: 'FluxGrid',

		components: {
			FluxCube
		},

		data: () => ({
			numTiles: 0,
			tile: {
				width: 1,
				height: 1,
				remainder: {
					width: 0,
					height: 0
				}
			},
			style: {
				position: 'absolute',
				width: '100%',
				height: '100%',
				zIndex: '12'
			}
		}),

		props: {
			slider: { type: Object, required: true },
			numRows: { type: Number, required: true },
			numCols: { type: Number, required: true },
			index: { type: Object, required: true },
			tileCss: { type: Object, default: () => {} }
		},

		computed: {
			tiles: function() {
				return this.$refs.tiles;
			}
		},

		created() {
			this.numTiles = this.numRows * this.numCols;

			this.tile.width = Math.floor(this.slider.size.width / this.numCols);
			this.tile.height = Math.floor(this.slider.size.height / this.numRows);

			this.tile.remainder.width = this.slider.size.width % this.numCols;
			this.tile.remainder.height = this.slider.size.height % this.numRows;
		},

		methods: {
			getRow(i) {
				let row = Math.floor(i / this.numCols);

				return row;
			},

			getCol(i) {
				let col = i % this.numCols;

				return col;
			},

			getTileCss(i) {
				i--;

				let row = this.getRow(i);
				let col = this.getCol(i);

				let width = this.tile.width + (col < this.tile.remainder.width? 1 : 0);
				let height = this.tile.height + (row < this.tile.remainder.height? 1 : 0);

				let top = row * this.tile.height + (row < this.tile.remainder.height? row : this.tile.remainder.height);
				let left = col * this.tile.width + (col < this.tile.remainder.width? col : this.tile.remainder.width);

				let zIndex = i + 1 < this.numCols / 2? 13 + i : 13 + this.numCols - i;

				return Object.assign({}, this.tileCss, {
					width: width +'px',
					height: height +'px',
					top: top +'px',
					left: left +'px',
					zIndex: zIndex
				});
			},

			setCss(css) {
				this.style = Object.assign({}, this.style, css);
			},

			transform(func) {
				this.$nextTick(() => {
					this.tiles.forEach((tile, i) => func(tile, i));
				});
			}
		}
	};
</script>
