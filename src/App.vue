<template>
	<input type="file" @change="uploadFile" />
	<p v-if="uploadStatus">{{ uploadStatus }}</p>
	<progress v-if="uploadProgress > 0" :value="uploadProgress" max="100"/>
	<p v-if="errorMessage" style="color: red">{{ errorMessage }}</p>
	<button v-if="key.length > 0" @click="copyToClipboard">Copy to clipboard</button>
</template>

<script>
export default {
	name: 'App',

	data: () => ({
		selectedFile: null,
		uploadStatus: '',
		key: '',
		uploadProgress: 0,
		errorMessage: "",
	}),

	methods: {
		uploadFile(event) {
			this.errorMessage = ""
			const file = event.target.files[0]
			if (!file) return
			const formData = new FormData()
			formData.append("file", file)
			const xhr = new XMLHttpRequest()
			xhr.open("POST", "/upload", true)
			xhr.upload.addEventListener("progress", (event) => {
				if (event.lengthComputable) {
					this.uploadProgress = Math.round((event.loaded / event.total) * 100)
				}
			})
			xhr.addEventListener("load", () => {
				if (xhr.status === 200) {
					this.key = JSON.parse(xhr.responseText).key
					this.uploadProgress = 100
				} else {
					this.errorMessage = `Upload failed with status: ${xhr.status} - ${xhr.statusText}`
				}
			})
			xhr.addEventListener("error", () => {
				this.errorMessage = "Error uploading file. Please check server logs."
			})
			xhr.addEventListener("abort", () => {
				this.errorMessage = "Upload aborted."
			})
			try {
				xhr.send(formData)
			} catch (error) {
				this.errorMessage = `Exception occurred: ${error.message}`
				console.error(this.errorMessage)
			}
		},

		copyToClipboard() {
			navigator.clipboard.writeText(`${window.location.protocol}//${window.location.host}/${this.key}`)
					.catch(err => {
						console.error('Error copying to clipboard:', err)
						this.uploadStatus = 'Error while copying'
					})
		}
	},

}
</script>

<style>
</style>
