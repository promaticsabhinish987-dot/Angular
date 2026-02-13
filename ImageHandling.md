### 1. Show default image if we have no image in user object. (display image)

```html

<img
  [src]="user?.profileImage || 'assets/default.png'"
  width="50"
  height="50"
  loading="lazy"
  class="rounded-circle"
/>

```


### 2. Upload and preview 

```html

<form [formGroup]="userForm" (ngSubmit)="submit()">
  <input type="file" (change)="onFileSelect($event)" accept="image/*">

  <div *ngIf="previewUrl">
    <img [src]="previewUrl" width="150">
  </div>

  <button type="submit">Submit</button>
</form>


userForm = this.fb.group({
  name: [''],
  image: [null]
});

previewUrl: string | ArrayBuffer | null = null;

onFileSelect(event: any) {
  const file = event.target.files[0];

  if (!file) return;

  this.userForm.patchValue({ image: file });
  this.userForm.get('image')?.updateValueAndValidity();

  const reader = new FileReader();
  reader.onload = () => this.previewUrl = reader.result;
  reader.readAsDataURL(file);
}

```

### 3.Custom Image Validators

File size validator
File type validator


### 4. Upload with Progress Bar

### 5. Multiple Image Upload (Gallery Style)

### 6. Drag and Drop Upload
### 7. Replacing old image


### 8. Image Compression Before Upload (Huge Optimization)

### 9. Cancel Upload Feature

### 10. Signed URL Upload (Enterprise Pattern)
Instead of sending file to backend:

Angular requests signed URL

Backend returns temporary S3 URL

Angular uploads directly to S3

Backend saves URL

Benefits:

Scalable

Reduces backend load

Used in high-scale systems
















