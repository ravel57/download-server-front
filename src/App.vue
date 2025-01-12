<template>
	<input type="file" @change="onFileChange" />
	<button @click="uploadFile">upload</button>
	<p v-if="uploadStatus">{{ uploadStatus }}</p>
	<a :href="getUrl" v-if="key.length > 0">Download</a>
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
		}
	},

	computed: {
		getUrl() {
			return `${window.location.protocol}://${window.location.host}/${this.key}`
		}
	}
}
</script>

<style>
</style>
