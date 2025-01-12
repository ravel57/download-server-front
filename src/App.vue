<template>
	<input type="file" @change="onFileChange" />
	<button @click="uploadFile">upload</button>
	<p v-if="uploadStatus">{{ uploadStatus }}</p>
</template>

<script>
import axios from "axios";

export default {
	name: 'App',

	data: () => ({
		selectedFile: null,
		uploadStatus: '',
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
				const response = axios.post('/upload', formData, {
					headers: {
						'Content-Type': 'multipart/form-data'
					}
				});
				this.uploadStatus = 'File uploaded successfully';
			} catch (error) {
				console.error('File upload error:', error);
				this.uploadStatus = 'File uploading error';
			}
		}
	}
}
</script>

<style>
</style>
