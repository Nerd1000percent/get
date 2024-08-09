this.diagram.nodeTemplate =
  $(go.Node, "Auto",
    $(go.Shape, "RoundedRectangle",
      { fill: "lightblue", strokeWidth: 0 }),
    $(go.TextBlock,
      { margin: 8 },
      new go.Binding("text", "key")),
    $("Button",
      {
        click: (e, obj) => this.onNodeButtonClick(obj)  // Event handler for the button
      },
      $(go.TextBlock, "Click Me")  // Button label
    )
  );




import { Component, ViewChild } from '@angular/core';
import { ConfirmationDialogComponent } from './confirmation-dialog/confirmation-dialog.component';

@Component({
  selector: 'app-diagram',
  templateUrl: './diagram.component.html',
  styleUrls: ['./diagram.component.css']
})
export class DiagramComponent {

  @ViewChild(ConfirmationDialogComponent) confirmationDialog: ConfirmationDialogComponent;

  constructor() { }

  // Method to show the confirmation dialog for saving
  showSaveConfirmation() {
    this.confirmationDialog.confirmAction(
      'Save Confirmation',
      'Do you want to save the diagram?',
      () => this.saveToFile()
    );
  }

  // Method to show the confirmation dialog for submitting
  checkAndSubmit() {
    if (this.diagram.model.nodeDataArray.length === 0) {
      this.confirmationDialog.confirmAction(
        'Submit Confirmation',
        'The diagram is empty. Cannot submit an empty diagram.',
        () => {
          // Handle submission here if any additional logic is needed
          this.submitDiagram();
        }
      );
    } else {
      this.submitDiagram();
    }
  }

  saveToFile() {
    const json = this.diagram.model.toJson();
    const blob = new Blob([json], { type: 'application/json' });
    const url = window.URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'diagram.json';
    a.click();
    window.URL.revokeObjectURL(url);
  }

  submitDiagram() {
    console.log("Diagram submitted successfully!");
  }
}



import { Component } from '@angular/core';
import { ConfirmationService, MessageService } from 'primeng/api';

@Component({
  selector: 'app-confirmation-dialog',
  templateUrl: './confirmation-dialog.component.html',
  styleUrls: ['./confirmation-dialog.component.css']
})
export class ConfirmationDialogComponent {

  constructor(
    private confirmationService: ConfirmationService,
    private messageService: MessageService
  ) {}

  confirmAction(header: string, message: string, onConfirm: () => void) {
    this.confirmationService.confirm({
      header: header,
      message: message,
      icon: 'pi pi-exclamation-triangle',
      accept: () => {
        onConfirm();
        this.messageService.add({ severity: 'info', summary: 'Confirmed', detail: 'Action confirmed' });
      },
      reject: () => {
        this.messageService.add({ severity: 'warn', summary: 'Cancelled', detail: 'Action cancelled' });
      }
    });
  }
}



import { Component, EventEmitter, Input, Output } from '@angular/core';

@Component({
  selector: 'app-confirmation-dialog',
  templateUrl: './confirmation-dialog.component.html',
  styleUrls: ['./confirmation-dialog.component.css']
})
export class ConfirmationDialogComponent {

  private _visible: boolean = false;

  @Input() 
  get visible(): boolean {
    return this._visible;
  }

  @Output() visibleChange: EventEmitter<boolean> = new EventEmitter<boolean>();

  set visible(value: boolean) {
    this._visible = value;
    this.visibleChange.emit(this._visible);
  }

  @Input() header: string = 'Confirmation';
  @Input() message: string = '';
  @Input() icon: string = 'pi pi-exclamation-triangle';
  @Input() isSubmitCheck: boolean = false;

  @Output() onConfirm: EventEmitter<void> = new EventEmitter();
  @Output() onCancel: EventEmitter<void> = new EventEmitter();

  confirm() {
    this.onConfirm.emit();
    this.visible = false;
  }

  cancel() {
    this.onCancel.emit();
    this.visible = false;
  }
}



import { Component, EventEmitter, Input, Output } from '@angular/core';

@Component({
  selector: 'app-confirmation-dialog',
  templateUrl: './confirmation-dialog.component.html',
  styleUrls: ['./confirmation-dialog.component.css']
})
export class ConfirmationDialogComponent {

  @Input() visible: boolean = false;
  @Input() header: string = 'Confirmation';
  @Input() message: string = '';
  @Input() icon: string = 'pi pi-exclamation-triangle';
  @Input() isSubmitCheck: boolean = false;

  @Output() onConfirm: EventEmitter<void> = new EventEmitter();
  @Output() onCancel: EventEmitter<void> = new EventEmitter();

  confirm() {
    this.onConfirm.emit();
    this.visible = false;
  }

  cancel() {
    this.onCancel.emit();
    this.visible = false;
  }
}



<p-confirmDialog [(visible)]="visible" [header]="header" [icon]="icon">
  <p>{{ message }}</p>
  <p-footer *ngIf="isSubmitCheck">
    <button pButton label="OK" (click)="confirm()"></button>
  </p-footer>
  <p-footer *ngIf="!isSubmitCheck">
    <button pButton label="Yes" (click)="confirm()"></button>
    <button pButton label="No" (click)="cancel()" class="ui-button-secondary"></button>
  </p-footer>
</p-confirmDialog>



import { Component } from '@angular/core';
import * as go from 'gojs';

@Component({
  selector: 'app-diagram',
  templateUrl: './diagram.component.html',
  styleUrls: ['./diagram.component.css']
})
export class DiagramComponent {

  public displayDialog: boolean = false;
  public displayConfirmationDialog: boolean = false;
  public nodeText: string = '';
  public isSubmitCheck: boolean = false;
  private pendingGroup: go.Part | null = null;
  private diagram: go.Diagram;

  constructor() { }

  ngOnInit() {
    const $ = go.GraphObject.make;

    this.diagram = $(go.Diagram, 'myDiagramDiv', {
      'undoManager.isEnabled': true
    });

    // Define the group template
    this.diagram.groupTemplate =
      $(go.Group, 'Auto',
        {
          selectable: true,
          resizable: true,
          minSize: new go.Size(100, 50),
          layout: $(go.GridLayout),
          isSubGraphExpanded: true,
          ungroupable: true,
          groupable: true
        },
        $(go.Shape, 'Rectangle',
          { strokeWidth: 2 },
          new go.Binding('fill', 'color')
        ),
        $(go.Panel, 'Vertical',
          $(go.TextBlock, { margin: 8, editable: true, name: "TEXT_BLOCK" },
            new go.Binding('text', 'key')),
          $(go.Placeholder, { padding: 5 })
        )
      );

    // Handle node drop event
    this.diagram.addDiagramListener('ExternalObjectsDropped', (e) => {
      this.onGroupDropped(e);
    });

    // Initialize diagram model
    this.diagram.model = new go.GraphLinksModel(
      [
        { key: 'Group1', color: 'lightblue', isGroup: true, shape: 'Rectangle' },
        { key: 'Beta', color: 'orange', group: 'Group1' }
      ],
      [
        { from: 'Beta', to: 'Group1' }
      ]);
  }

  onGroupDropped(e: go.DiagramEvent) {
    const droppedGroups = e.subject.parts;
    droppedGroups.each((part) => {
      const group = part.data;
      if (group.shape === 'Rectangle' && group.isGroup) {
        this.pendingGroup = part;
        this.nodeText = group.key;
        this.displayDialog = true;
        this.diagram.currentTool.doCancel();
      }
    });
  }

  confirmDrop() {
    if (this.pendingGroup) {
      this.diagram.model.setDataProperty(this.pendingGroup.data, 'key', this.nodeText);
      this.pendingGroup.isSubGraphExpanded = false;
      this.pendingGroup.ungroupable = false;
      this.pendingGroup.groupable = false;

      const textBlock = this.pendingGroup.findObject('TEXT_BLOCK') as go.TextBlock;
      if (textBlock) {
        textBlock.editable = false;
      }

      this.pendingGroup.selectable = false;
      this.pendingGroup.resizable = false;
      this.pendingGroup.movable = false;
      this.pendingGroup.deletable = false;

      this.pendingGroup = null;
    }
    this.displayDialog = false;
  }

  cancelDrop() {
    this.pendingGroup = null;
    this.displayDialog = false;
  }

  // Save the diagram data to a file
  saveToFile() {
    const json = this.diagram.model.toJson();
    const blob = new Blob([json], { type: 'application/json' });
    const url = window.URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'diagram.json';
    a.click();
    window.URL.revokeObjectURL(url);
  }

  // Show the confirmation dialog before saving
  showSaveConfirmation() {
    this.isSubmitCheck = false;  // Reset the check flag
    this.displayConfirmationDialog = true;
  }

  // Check if the diagram has any nodes before submission
  checkAndSubmit() {
    if (this.diagram.model.nodeDataArray.length === 0) {
      this.isSubmitCheck = true;  // Indicate this is for submit check
      this.displayConfirmationDialog = true;  // Show the dialog to warn about the empty diagram
    } else {
      this.submitDiagram();  // If the diagram has nodes, proceed with submission
    }
  }

  // Proceed with submission logic
  submitDiagram() {
    // Implement the actual submission logic here
    console.log("Diagram submitted successfully!");
  }

  // Handle the confirmation action
  handleConfirm() {
    if (this.isSubmitCheck) {
      this.displayConfirmationDialog = false;
    } else {
      this.saveToFile();
    }
  }

  // Handle the cancel action
  handleCancel() {
    this.displayConfirmationDialog = false;
  }
}

<!-- Dialog for editing group text -->
<p-dialog [(visible)]="displayDialog" modal="true" header="Edit Group Text">
    <div>
        <label for="groupText">Enter new group text:</label>
        <input id="groupText" type="text" [(ngModel)]="nodeText" />
    </div>
    <p-footer>
        <button pButton label="OK" (click)="confirmDrop()"></button>
        <button pButton label="Cancel" (click)="cancelDrop()" class="ui-button-secondary"></button>
    </p-footer>
</p-dialog>

<!-- Diagram container -->
<div id="myDiagramDiv" style="width:100%; height:400px; border:1px solid black"></div>

<!-- Save Button -->
<button pButton label="Save Diagram" (click)="showSaveConfirmation()"></button>

<!-- Submit Button -->
<button pButton label="Submit Diagram" (click)="checkAndSubmit()"></button>

<!-- Confirmation Dialog Component -->
<app-confirmation-dialog
  [visible]="displayConfirmationDialog"
  [header]="isSubmitCheck ? 'Submit Confirmation' : 'Save Confirmation'"
  [message]="isSubmitCheck ? 'The diagram is empty. Cannot submit an empty diagram.' : 'Do you want to save the diagram?'"
  [isSubmitCheck]="isSubmitCheck"
  (onConfirm)="handleConfirm()"
  (onCancel)="handleCancel()"
></app-confirmation-dialog>


