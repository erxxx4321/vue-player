<template>
	<div class="player" :class="{ playing: isPlaying }">
		<!-- Dashboard -->
		<div class="dashboard">
			<!-- Header -->
			<header>
				<h4>Now playing:</h4>
				<h2 class="song-name">{{ current.title }}</h2>
			</header>

			<!-- CD -->
			<div class="cd">
				<div class="cd-thumb" :style="{ 'background-image': `url(${current.img})` }"></div>
			</div>

			<!-- Control -->
			<div class="control">
				<div class="btn btn-repeat" :class="{ active: isRepeat }">
					<i class="fas fa-redo" @click="repeat"></i>
				</div>
				<div class="btn btn-prev">
					<i class="fas fa-step-backward" id="prev" @click="prev"></i>
				</div>
				<div class="btn btn-toggle-play">
					<i class="fas fa-pause icon-pause" id="pause" @click="pause"></i>
					<i class="fas fa-play icon-play" id="playbtn" @click="play"></i>
				</div>
				<div class="btn btn-new">
					<i class="fas fa-step-forward" id="next" @click="next"></i>
				</div>
				<div class="btn btn-random" :class="{ active: isRandom }">
					<i class="fas fa-random" @click="random"></i>
				</div>
			</div>

			<input id="progress" class="progress" type="range" value="0" step="0.1" min="0" max="100" />

			<!-- Playlist -->
			<div class="playlist">
				<div
					class="song"
					v-for="(s, index) in songs"
					:key="index"
					@click="pick(s, index)"
					:class="[current == s ? 'active' : '']"
				>
					<div class="thumb" :style="{ 'background-image': `url(${s.img})` }"></div>
					<div class="body">
						<h3 class="title">{{ s.title }}</h3>
						<p class="author">{{ s.singer }}</p>
					</div>
					<div class="option">
						<i class="fas fa-ellipsis-h"></i>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
	export default {
		name: 'Player',
		props: {
			msg: String,
		},
		data() {
			return {
				current: {},
				index: 0,
				isRepeat: false,
				isRandom: false,
				isPlaying: false,
				sound: new Audio(),
				songs: [
					{
						title: 'Thunder',
						singer: 'Imagine Dragons',
						src: './assets/Imagine Dragons - Thunder.mp3',
						img: './assets/thunder.jpeg',
					},
					{
						title: 'West Coast',
						singer: 'Imagine Dragons',
						src: './assets/Imagine Dragons - West Coast.mp3',
						img: './assets/westcost.jpeg',
					},
					{
						title: 'Demons',
						singer: 'Imagine Dragons',
						src: './assets/Imagine Dragons - Demons.mp3',
						img: './assets/demon.jpeg',
					},
					{
						title: '我是一隻魚 Im a fish',
						singer: 'Sunset Rollercoaster',
						src: './assets/Sunset Rollercoaster - 我是一隻魚 Im a fish.mp3',
						img: './assets/Im_a_fish.jpeg',
					},
				],
			};
		},
		methods: {
			play(song) {
				if (typeof song.src != 'undefined') {
					this.current = song;
					this.sound.src = this.current.src;
				}

				this.sound.play();
				this.isPlaying = true;
			},
			pause() {
				this.sound.pause();
				this.isPlaying = false;
			},
			next() {
				if (this.isRandom) {
					let newInt = Math.floor(Math.random() * this.songs.length);
					while (this.index == newInt) {
						newInt = Math.floor(Math.random() * this.songs.length);
					}
					this.index = newInt;
					this.play(this.current);
				} else {
					this.index++;
					if (this.index > this.songs.length - 1) {
						this.index = 0;
					}
				}
				this.current = this.songs[this.index];
				this.play(this.current);
			},
			prev() {
				this.index--;
				if (this.index < 0) {
					this.index = this.songs.length - 1;
				}
				this.current = this.songs[this.index];
				this.play(this.current);
			},
			pick(song, index) {
				this.isPlaying = true;
				this.index = index;
				this.current = song;
				this.sound.src = this.current.src;
				this.sound.play();
			},
			repeat() {
				this.isRepeat = !this.isRepeat;
			},
			random() {
				this.isRandom = !this.isRandom;
			},
		},
		created() {
			this.current = this.songs[this.index];
			this.sound.src = this.current.src;

			this.sound.addEventListener('timeupdate', () => {
				document.getElementById('progress').value = Math.floor((this.sound.currentTime / this.sound.duration) * 100);
				document.getElementById('progress').addEventListener('change', (e) => {
					this.sound.currentTime = (e.target.value / 100) * this.sound.duration;
				});
			});
			this.sound.addEventListener('ended', () => {
				if (this.isRandom) {
					let newInt = Math.floor(Math.random() * this.songs.length);
					while (this.index == newInt) {
						newInt = Math.floor(Math.random() * this.songs.length);
					}
					this.index = newInt;
					this.current = this.songs[this.index];
					this.sound.src = this.current.src;
					this.play(this.current);
				} else if (this.isRepeat) {
					this.index++;
					if (this.index > this.songs.length - 1) this.index = 0;
					this.current = this.songs[this.index];
					this.sound.src = this.current.src;
					this.play(this.current);
				} else {
					this.index++;
					if (this.index > this.songs.length - 1) {
						this.index = 0;
						this.isPlaying = false;
						this.current = this.songs[this.index];
						this.sound.src = this.current.src;
					} else {
						this.current = this.songs[this.index];
						this.play(this.current);
					}
				}
			});
		},
	};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
	:root {
		--primary-color: #fca311;
		--text-color: #14213d;
	}

	* {
		padding: 0;
		margin: 0;
		box-sizing: inherit;
	}

	body {
		background-color: #f5f5f5;
	}

	html {
		box-sizing: border-box;
		font-family: 'Poppins', sans-serif;
	}

	.player {
		position: relative;
		max-width: 480px;
		margin: 0 auto;
	}

	.player .icon-pause {
		display: none;
	}

	.player.playing .icon-pause {
		display: inline-block;
	}

	.player.playing .icon-play {
		display: none;
	}

	.dashboard {
		padding: 16px 16px 14px;
		background-color: #fff;
		position: fixed;
		top: 0;
		width: 100%;
		max-width: 480px;
		border-bottom: 1px solid #ebebeb;
	}

	/* HEADER */
	header {
		text-align: center;
		margin-bottom: 10px;
	}

	header h4 {
		color: var(--primary-color);
		font-size: 12px;
	}

	header h2 {
		color: var(--text-color);
		font-size: 20px;
	}

	/* CD */
	.cd {
		display: flex;
		margin: auto;
		width: 200px;
	}

	.cd-thumb {
		width: 100%;
		padding-top: 100%;
		border-radius: 50%;
		background-color: #333;
		background-size: cover;
		margin: auto;
	}

	/* CONTROL */
	.control {
		display: flex;
		align-items: center;
		justify-content: space-around;
		padding: 18px 0 8px 0;
	}

	.control .btn {
		color: #666;
		padding: 18px;
		font-size: 18px;
	}

	.control .btn:active {
		color: var(--primary-color);
	}

	.btn.active {
		color: var(--primary-color);
	}

	.control .btn:hover {
		cursor: pointer;
	}
	.control .btn-toggle-play {
		width: 56px;
		height: 56px;
		border-radius: 50%;
		font-size: 24px;
		color: #fff;
		display: flex;
		align-items: center;
		justify-content: center;
		background-color: var(--primary-color);
	}

	.progress {
		width: 100%;
		-webkit-appearance: none;
		height: 6px;
		background: #d3d3d3;
		outline: none;
		opacity: 0.7;
		-webkit-transition: 0.2s;
		transition: opacity 0.2s;
	}

	.progress::-webkit-slider-thumb {
		-webkit-appearance: none;
		appearance: none;
		width: 12px;
		height: 6px;
		background-color: var(--primary-color);
		cursor: pointer;
	}

	/* PLAYLIST */
	.playlist {
		/* margin-top: 408px; */
		padding: 12px;
	}

	.song {
		display: flex;
		align-items: center;
		margin-bottom: 12px;
		background-color: #fff;
		padding: 8px 16px;
		border-radius: 5px;
		box-shadow: 0 2px 3px rgba(0, 0, 0, 0.1);
	}

	.song:hover {
		cursor: pointer;
	}

	.song.active {
		background-color: var(--primary-color);
	}

	.song:active {
		opacity: 0.8;
	}

	.song.active .option,
	.song.active .author,
	.song.active .title {
		color: #fff;
	}

	.song .thumb {
		width: 44px;
		height: 44px;
		border-radius: 50%;
		background-size: cover;
		margin: 0 8px;
	}

	.song .body {
		flex: 1;
		padding: 0 16px;
	}

	.song .title {
		font-size: 18px;
		color: var(--text-color);
	}

	.song .author {
		font-size: 12px;
		color: var(--text-color);
	}

	.song .option {
		padding: 16px 8px;
		color: #999;
		font-size: 18px;
	}
</style>
