<template>
	<div class="post-comment">
		<div class="frow justify-start nowrap">
			<Avatar
				v-tooltip="poster.name"
				:name="poster.name"
				:color="poster.color"
				size="md"
				class="post-comment__avatar"
			/>
			<div class="post-comment__content">
				<span v-html="formattedContent"></span>
				<div class="post-comment__post-time">{{ postTime }}</div>
			</div>
		</div>
	</div>
</template>

<script>
import showdown from 'showdown';
import Avatar from '@components/Avatar';
const converter = new showdown.Converter({
	simplifiedAutoLink: true,
});
export default {
	components: {
		Avatar,
	},
	props: {
		comment: Object,
	},
	data() {
		return {
			lastUpdateTimestamp: moment(),
		};
	},
	mounted() {
		setInterval(() => {
			this.lastUpdateTimestamp = moment();
		}, 3000);
	},
	computed: {
		formattedContent() {
			return converter.makeHtml(this.comment.content);
		},
		poster() {
			return this.getUserById(this.comment.posterId) || {};
		},
		postTime() {
			if(!this.comment.timestamp) {
				return 'posting';
			}
			return this.lastUpdateTimestamp && moment(this.comment.timestamp.toDate()).fromNow();
		},
		...mapGetters('board', [
			'getUserById',
		]),
	},
};
</script>