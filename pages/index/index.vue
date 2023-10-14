<template>
	<div class="sgs_container">
		<div class="sgs_wrapper">
		    <view class="status_bar">
		        <!-- 这里是状态栏 -->
		    </view>
		
			<label class="required" for="suggestion">请输入你的建议：<br>Please enter your suggestion:</label>
			<textarea :class="errorClass" placeholder="对本条的建议，或者添加你对别人的鼓励 / Suggestion or add one" id="suggestion" type="text" v-model="suggestion_text"></textarea>
		
			<label for="feedback">我该通过什么向你反馈？<br>What should I give you feedback through?</label>
			<input placeholder="QQ、Wechat、WhatsApp、Email or Other" id="feedback" type="text" v-model="contact" />
		
			<button v-if="loading" class="sgs_submit">Loading...</button>
			<button v-else class="sgs_submit" @click="handleSubmit">提交 / Submit</button>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				suggestion_text: '',
				contact: '',
				errorClass: {}, 
				timer: null,
				loading: false,
			}
		},
		methods: {
			handleSubmit() {
				if (this.timer) {
					clearTimeout(this.timer);
				}
				this.timer = setTimeout(this.submit, 100);
			},

			submit() {
				if (!this.suggestion_text) {
					this.errorClass = 'error_tip';
					setTimeout(() => {
						this.errorClass = '';
					}, 1000)
					return;
				}
				this.loading = true;

				const db = uniCloud.database();
				const collection = db.collection('suggestion_list');
				collection.add({
					suggestion_text: this.suggestion_text,
					contact: this.contact || '无名氏',
				}).then(res => {
					this.loading = false;
					alert('Success');
					window.location.reload();
				}).catch(e => {
					this.loading = false;
					alert('Something wrong! I guess maybe my server has expired.');
				})
			}
		}
	}
</script>

<style lang="scss">
    .status_bar {
        height: var(--status-bar-height);
        width: 100%;
    }
	@keyframes error_tip_animation {
		25% {
			transform: scale(1.01);
			border-color: red;
		}
		50% {
			transform: scale(1);
			border-color: red;
		}
		75% {
			transform: scale(1.01);
			border-color: red;
		}
		100% {
			transform: scale(1);
		}
	}
	.sgs_container {
		overflow: hidden;
	}
	.sgs_wrapper {
		width: 50%;
		margin: 15vh auto 0;
		.required {
			&::before {
				content: '*';
				color: red;
				width: 10rpx;
				margin: 0 5rpx 0 -20rpx;
			}
		}
		input {
			height: 80rpx;
		}
		textarea {
			&.error_tip {
				animation: error_tip_animation .8s linear;
			}
		}
		input, textarea {
			width: 100%;
			padding: 10rpx 20rpx;
			margin: 40rpx 0;
			line-height: normal;
			box-sizing: border-box;
			border: 1px solid $uni-color-primary;
			border-radius: 6rpx;
		}
		.sgs_submit {
			width: 100%;
			margin-top: 60rpx;
			border: none;
			background-color: $uni-color-primary;
			color: #fff;
			border-radius: 6rpx;
		}
	}
	@media screen and (max-width: 800px) {
		.sgs_wrapper {
			width: 80%;
		}
	}
</style>
