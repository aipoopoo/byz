<template>
	<div class="post-comment-form">
		<div class="frow nowrap justify-start items-start">
			<Avatar
				:name="currentUser.name"
				:color="currentUser.color"
				size="md"
				class="shrink-0 mr-10"
			/>
			<textarea
				v-model.trim="content"
				v-auto-height
				ref="input"
				class="post-comment-form__textarea grow-remain"
				type="text"
				rows="1"
				placeholder="Any comment?"
				@keypress.enter="submit"
			></textarea>
		</div>
	</div>
</template>

<script>
import { scrollTopAnimate } from '@libs/uiUtils';
import { EventBus } from '@/main';
import Avatar from '@components/Avatar';
export default {
	components: {
		Avatar,
	},
	props: {
		postId: {
			type: String,
			required: true,
		},
	},
	data() {
		return {
			content: '',
		};
	},
	computed: {
		isSubmittable() {
			return this.content.length > 0;
		},
		...mapGetters('board', [
			'currentUser',
		]),
	},
	methods: {
		async submit(e) {
			if(e.ctrlKey || e.shiftKey || e.altKey) {
				// 換行
				return;
			}
			e.preventDefault();
			if(!this.isSubmittable) {
				return;
			}
			this.createComment({
				postId: this.postId,
				comment: {
					content: this.content,
					posterId: this.currentUser.id,
				},
			});
			this.content = '';
			this.$refs.input.style.height = 'auto';
			this.$refs.input.focus();

			EventBus.$once('added.comment', () => {
				this.$nextTick(this.scrollPostToBottom);
			});
		},
		scrollPostToBottom() {
			const postElement = document.querySelector(`[data-post-id="${ this.postId }"]`);
			scrollTopAnimate(postElement, postElement.scrollHeight - postElement.clientHeight);
		},
		...mapActions('board', [
			'createComment'
		]),
	},
};
</script>
