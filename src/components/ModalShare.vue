<template>
	<ModalContent
		:is-open="isOpen"
		size="lg"
		class="modal-share"
		@close="$emit('close')">
		<div slot="body" class="modal-share__body">
			<input
				:value="shareLink"
				type="text"
				class="modal-share__input"
				readonly
				@click="onClickCopy"
			>
			<button class="btn btn--primary" @click="onClickCopy">
				<span v-if="isCopied">Copied!</span>
				<i v-else class="post__tool-item las la-copy"></i>
			</button>
		</div>
		<div v-if="!isGuestMode" slot="footer" class="modal-share__footer">
			<div class="mr-20">
				Invite people to
			</div>
			<div class="frow row-start mr-20">
				<input
					id="option-join"
					v-model="linkType"
					type="radio"
					value="join"
				>
				<label for="option-join" class="px-10">join</label>
			</div>
			<div class="frow row-start">
				<input
					id="option-guest"
					v-model="linkType"
					type="radio"
					value="guest"
				>
				<label for="option-guest" class="px-10">view</label>
			</div>
		</div>
	</ModalContent>
</template>

<script>
import setStringToClipBoard from 'set-string-to-clipboard';
import ModalContent from '@components/ModalContent';
export default {
	components: {
		ModalContent,
	},
	props: ['isOpen'],
	data() {
		return {
			isCopied: false,
			linkType: 'join',
		};
	},
	computed: {
		shareLink() {
			const link = `${ window.location.protocol }//${ window.location.host }/board/${ this.boardId }`;
			if(this.isGuestMode) {
				return link;
			}
			return this.linkType === 'join' ? `${ link }/join` : link;
		},
		...mapState('board', [
			'isGuestMode',
		]),
		...mapGetters('board', [
			'boardId',
		]),
	},
	methods: {
		onClickCopy() {
			if(this.isCopied) {
				return;
			}
			setStringToClipBoard(this.shareLink);
			this.isCopied = true;
			setTimeout(() => {
				this.isCopied = false;
			}, 1000);
		},
	},
};
</script>
