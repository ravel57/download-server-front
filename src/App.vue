<template>
	<input type="file" @change="onFileChange" />
	<button @click="uploadFile">upload</button>
	<p v-if="uploadStatus">{{ uploadStatus }}</p>
	<a v-if="key.length > 0" :href="getUrl">Download</a>
	<button v-if="key.length > 0" @click="copyToClipboard">Copy</button>
</template>

<script>
import axios from "axios";

export default {
	name: 'App',

	data: () => ({
		selectedFile: null,
		uploadStatus: '',
		key: '',
	}),

	methods: {
		onFileChange(event) {
			this.selectedFile = event.target.files[0];
		},

		async uploadFile() {
			if (!this.selectedFile) {
				this.uploadStatus = 'Select file before uploading';
				return;
			}

			const formData = new FormData();
			formData.append('file', this.selectedFile);

			try {
				await axios.post('/upload', formData, {
					headers: {
						'Content-Type': 'multipart/form-data'
					}
				}).then((response) => {
					this.key =  response.data.key;
				});
			} catch (error) {
				console.error('File upload error:', error);
				this.uploadStatus = 'File uploading error';
			}
		},

		copyToClipboard() {
			if (!this.selectedFile) {
				this.uploadStatus = 'There is no file to copy';
				return;
			}

			navigator.clipboard.writeText(this.selectedFile.name)
				.then(() => {
					this.uploadStatus = 'The file name has been copied to the clipboard.';
				})
				.catch(err => {
					console.error('Error copying to clipboard:', err);
					this.uploadStatus = 'Error while copying';
				});
		}
	},

	computed: {
		getUrl() {
			return `${window.location.protocol}${this.key}`
		}
	}
}
</script>

<style>
</style>
