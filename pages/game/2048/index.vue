<template>
	<view class="main">
		<view class="v-x" v-for="x in map">
			<text class="v-y" v-for="y in x">
				<text v-if="y!=0">{{y}}</text>
			</text>
		</view>
	</view>
</template>

<script setup>
	import {
		onMounted,
		onUnmounted,
		reactive,
		nextTick
	} from 'vue';

	const map = reactive(new Array(4).fill().map((e) => new Array(4).fill(0)))

	function deepClone(arr) {
		return JSON.parse(JSON.stringify(arr));
	}

	function isSame(arr, list) {
		let fleg = true
		arr.forEach((l, x) => {
			l.forEach((e, y) => {
				if (arr[x][y] != list[x][y]) {
					fleg = false
				}
			})
		})
		return fleg
	}
	async function handleKeyDown(event) {
		const oM = deepClone(map)
		switch (event.key) {
			case 'ArrowUp': // 向上箭头
			case 'w': // W
			case 'W': // 大写的W
				console.log('向上移动');
				map.forEach(l => {
					const t = l.filter(x => x != 0)
					const list = []
					let f = true
					while (f) {
						const h = t.shift()
						if (!h) {
							f = false
							continue;
						}
						if (t.length == 0) {
							list.push(h)
							f = false
							continue;
						}
						if (h != t[0]) {
							list.push(h)
							continue;
						} else {
							list.push(h + t.shift())
						}

					}
					while (list.length < l.length) {
						list.push(0);
					}
					for (var i = 0; i < l.length; i++) {
						l[i] = list[i]
					}
				})
				console.log(isSame(map, oM))

				if (!isSame(map, oM)) {
					addEle()
				}

				break;
			case 'ArrowDown': // 向下箭头
			case 's': // S
			case 'S': // 大写的S
				console.log('向下移动');
				map.forEach(l => {
					const t = l.filter(x => x != 0).reverse()
					const list = []
					let f = true
					while (f) {
						const h = t.shift()
						if (!h) {
							f = false
							continue;
						}
						if (t.length == 0) {
							list.push(h)
							f = false
							continue;
						}
						if (h != t[0]) {
							list.push(h)
							continue;
						}
						list.push(h + t.shift())
					}
					while (list.length < l.length) {
						list.push(0);
					}
					for (var i = 0; i < l.length; i++) {
						l[l.length - 1 - i] = list[i]
					}
				})
				if (!isSame(map, oM)) {
					addEle()
				}

				break;
			case 'ArrowLeft': // 向左箭头
			case 'a': // A
			case 'A': // 大写的A
				console.log('向左移动');
				yLine().forEach((l, k) => {
					const t = l.filter(x => x != 0)
					const list = []
					let f = true
					while (f) {
						const h = t.shift()
						if (!h) {
							f = false
							continue;
						}
						if (t.length == 0) {
							list.push(h)
							f = false
							continue;
						}
						if (h != t[0]) {
							list.push(h)
							continue;
						} else {
							list.push(h + t.shift())
						}

					}
					while (list.length < l.length) {
						list.push(0);
					}
					for (var i = 0; i < l.length; i++) {
						map[i][k] = list[i]
					}
				})
				if (!isSame(map, oM)) {
					addEle()
				}

				break;
			case 'ArrowRight': // 向右箭头
			case 'd': // D
			case 'D': // 大写的D
				console.log('向右移动');
				yLine().forEach((l, k) => {
					const t = l.filter(x => x != 0).reverse()
					const list = []
					let f = true
					while (f) {
						const h = t.shift()
						if (!h) {
							f = false
							continue;
						}
						if (t.length == 0) {
							list.push(h)
							f = false
							continue;
						}
						if (h != t[0]) {
							list.push(h)
							continue;
						} else {
							list.push(h + t.shift())
						}

					}
					while (list.length < l.length) {
						list.push(0);
					}
					for (var i = 0; i < l.length; i++) {
						map[l.length - 1 - i][k] = list[i]
					}
				})
				if (!isSame(map, oM)) {
					addEle()
				}

				break;
			default:
				break;
		}
	}

	function yLine() {
		const temp = new Array(4).fill().map(() => new Array(4).fill(0))
		temp.forEach((k, y) => {
			map.forEach((l, x) => {
				l.forEach((e, yo) => {
					if (y == yo) {
						k[x] = map[x][yo]
					}
				})
			})
		})
		return temp
	}

	function getEmptyEle() {
		let emptyEles = []
		map.forEach((l, x) => {
			l.forEach((e, y) => {
				if (map[x][y] == 0) {
					emptyEles.push([x, y])
				}
			})
		})
		return emptyEles
	}

	function getRandomIntInclusive(min, max) {
		min = Math.ceil(min);
		max = Math.floor(max);
		return Math.floor(Math.random() * (max - min + 1) + min); // The maximum is inclusive and minimum is inclusive
	}

	function fail() {
		map.forEach(l => {
			l.fill(0, 0, l.length)
		})
		alert("重新开始")
		start()
	}

	function addEle() {
		const eL = getEmptyEle()
		if (eL.length === 0) {
			fail()
		}
		const index = getRandomIntInclusive(0, eL.length - 1)
		const ele = eL[index]
		map[ele[0]][ele[1]] = Math.random() > 0.7 ? 4 : 2
	}

	function start() {
		addEle()
		addEle()
	}
	onMounted(() => {
		start()
		document.addEventListener('keydown', handleKeyDown);
	})
	onUnmounted(() => {
		document.removeEventListener('keydown', handleKeyDown);
	})
</script>

<style scoped>
	.main {
		height: 700rpx;
		width: 700rpx;
		display: grid;
		gap: 50rpx;
		grid-template-columns: 1fr 1fr 1fr 1fr;
	}

	.v-x {
		display: grid;
		gap: 50rpx;
		grid-template-rows: 1fr 1fr 1fr 1fr;
	}

	.v-y {
		display: grid;
		width: 150rpx;
		height: 150rpx;
		background-color: antiquewhite;
		align-content: center;
		justify-content: center;
	}
</style>