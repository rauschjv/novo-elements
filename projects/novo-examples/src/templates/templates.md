
Bullhorn Application Templates
------------------

Bullhorn provides customization of the user experience with custom tabs, custom cards, custom actions and more.

The [Bullhorn Extension Samples](https://github.com/bullhorn/extension-samples) repo
provides several examples for getting started building custom apps using Novo Elements.

## Resume intake template

Use Novo Elements form controls to capture a candidate resume payload that can be sent directly to Bullhorn. The example below wires up contact details, skills, prior experience, and a resume attachment into a single reactive form.

**TypeScript**

```ts
import { Component } from '@angular/core';
import { FormArray, FormBuilder, FormGroup, Validators } from '@angular/forms';
import { NovoToastService } from 'novo-elements';

@Component({
  selector: 'app-resume-template',
  templateUrl: './resume-template.html',
})
export class ResumeTemplateComponent {
  resumeForm: FormGroup;

  constructor(private fb: FormBuilder, private toaster: NovoToastService) {
    this.resumeForm = this.fb.group({
      fullName: ['', Validators.required],
      title: ['', Validators.required],
      email: ['', [Validators.required, Validators.email]],
      phone: ['', Validators.required],
      skills: [[]],
      resume: [null, Validators.required],
      experiences: this.fb.array([this.buildExperience()]),
    });
  }

  get experiences(): FormArray {
    return this.resumeForm.get('experiences') as FormArray;
  }

  buildExperience(): FormGroup {
    return this.fb.group({
      company: ['', Validators.required],
      role: ['', Validators.required],
      start: ['', Validators.required],
      end: [null],
      summary: [''],
    });
  }

  addExperience(): void {
    this.experiences.push(this.buildExperience());
  }

  submit(): void {
    if (this.resumeForm.invalid) {
      this.resumeForm.markAllAsTouched();
      return;
    }

    const payload = this.resumeForm.value;
    // TODO: POST payload to your Bullhorn endpoint or App Bridge action
    this.toaster.alert({
      title: 'Resume ready for Bullhorn',
      message: 'Send the payload to your integration endpoint.',
    });
    console.log(payload);
  }
}
```

**Template**

```html
<novo-form [formGroup]="resumeForm" layout="vertical">
  <novo-fields appearance="stacked" [fullWidth]="true">
    <novo-field>
      <novo-label>Full Name</novo-label>
      <input novoInput formControlName="fullName" placeholder="Candidate Name" />
    </novo-field>

    <novo-field>
      <novo-label>Headline</novo-label>
      <input novoInput formControlName="title" placeholder="Product Designer" />
    </novo-field>

    <novo-field>
      <novo-label>Email</novo-label>
      <input novoInput type="email" formControlName="email" placeholder="candidate@domain.com" />
    </novo-field>

    <novo-field>
      <novo-label>Phone</novo-label>
      <input novoInput type="tel" formControlName="phone" placeholder="555-123-4567" />
    </novo-field>

    <novo-field>
      <novo-label>Skills</novo-label>
      <novo-chips formControlName="skills" placeholder="Add skills"></novo-chips>
    </novo-field>

    <novo-field>
      <novo-label>Resume</novo-label>
      <novo-file-input
        formControlName="resume"
        accept=".pdf,.doc,.docx"
        [layoutOptions]="{ align: 'left' }"
      ></novo-file-input>
    </novo-field>

    <div formArrayName="experiences">
      <novo-field *ngFor="let exp of experiences.controls; index as i" [formGroupName]="i">
        <novo-label>Experience {{ i + 1 }}</novo-label>
        <novo-row gap="1rem">
          <input novoInput formControlName="company" placeholder="Company" />
          <input novoInput formControlName="role" placeholder="Role" />
        </novo-row>
        <novo-row gap="1rem">
          <input novoInput type="date" formControlName="start" placeholder="Start" />
          <input novoInput type="date" formControlName="end" placeholder="End" />
        </novo-row>
        <textarea novoInput formControlName="summary" placeholder="Impact, achievements, tech"></textarea>
      </novo-field>
    </div>

    <button type="button" theme="standard" (click)="addExperience()">Add Experience</button>
    <button type="button" theme="primary" (click)="submit()" [disabled]="resumeForm.invalid">Submit to Bullhorn</button>
  </novo-fields>
</novo-form>
```
