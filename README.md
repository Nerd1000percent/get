import { ComponentFixture, TestBed, waitForAsync } from '@angular/core/testing';
import { DiagramComponent } from './diagram.component';
import { By } from '@angular/platform-browser';
import * as go from 'gojs';

describe('DiagramComponent', () => {
  let component: DiagramComponent;
  let fixture: ComponentFixture<DiagramComponent>;
  let diagramDiv: HTMLElement;

  beforeEach(waitForAsync(() => {
    TestBed.configureTestingModule({
      declarations: [DiagramComponent],
    }).compileComponents();
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(DiagramComponent);
    component = fixture.componentInstance;
    diagramDiv = fixture.debugElement.query(By.css('div')).nativeElement;
    fixture.detectChanges();
    component.initializeDiagram(diagramDiv);
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should save the diagram', () => {
    spyOn(component, 'save').and.callThrough();
    const saveButton = fixture.debugElement.query(By.css('button')).nativeElement;
    saveButton.click();
    expect(component.save).toHaveBeenCalled();
  });

  it('should load the diagram', () => {
    spyOn(component, 'load').and.callThrough();
    const loadButton = fixture.debugElement.queryAll(By.css('button'))[1].nativeElement;
    loadButton.click();
    expect(component.load).toHaveBeenCalled();
  });

  it('should handle file input correctly', () => {
    spyOn(window, 'alert');
    const input = document.createElement('input');
    input.type = 'file';
    document.body.appendChild(input);

    const event = new Event('change');
    Object.defineProperty(event, 'target', {
      value: { files: [new Blob(['{}'], { type: 'application/json' })] },
    });

    input.dispatchEvent(event);
    component.load();
    expect(window.alert).not.toHaveBeenCalled();
    document.body.removeChild(input);
  });

  it('should handle no file selected', () => {
    spyOn(window, 'alert');
    const input = document.createElement('input');
    input.type = 'file';
    document.body.appendChild(input);

    const event = new Event('change');
    Object.defineProperty(event, 'target', {
      value: { files: [] },
    });

    input.dispatchEvent(event);
    component.load();
    expect(window.alert).toHaveBeenCalledWith('No file selected.');
    document.body.removeChild(input);
  });

  it('should handle multiple files selected', () => {
    spyOn(window, 'alert');
    const input = document.createElement('input');
    input.type = 'file';
    document.body.appendChild(input);

    const event = new Event('change');
    Object.defineProperty(event, 'target', {
      value: { files: [new Blob(['{}'], { type: 'application/json' }), new Blob(['{}'], { type: 'application/json' })] },
    });

    input.dispatchEvent(event);
    component.load();
    expect(window.alert).toHaveBeenCalledWith('Please select a single JSON file.');
    document.body.removeChild(input);
  });

  it('should handle non-JSON file selected', () => {
    spyOn(window, 'alert');
    const input = document.createElement('input');
    input.type = 'file';
    document.body.appendChild(input);

    const event = new Event('change');
    Object.defineProperty(event, 'target', {
      value: { files: [new Blob(['text'], { type: 'text/plain' })] },
    });

    input.dispatchEvent(event);
    component.load();
    expect(window.alert).toHaveBeenCalledWith('Please select a valid JSON file.');
    document.body.removeChild(input);
  });

  it('should show and hide ports on mouse enter/leave', () => {
    const nodeData = { key: 1, text: 'Node 1', loc: '0 0' };
    component.diagram.model = new go.GraphLinksModel([nodeData]);
    const node = component.diagram.findNodeForKey(1);

    // Simulate mouse enter
    const mouseEnterEvent = new go.InputEvent();
    mouseEnterEvent.targetDiagram = component.diagram;
    node.mouseEnter(mouseEnterEvent, node);

    node.ports.each((port) => {
      if (port.portId !== '') {
        expect(port.fill).toBe('rgba(0,0,0,.3)');
      }
    });

    // Simulate mouse leave
    const mouseLeaveEvent = new go.InputEvent();
    mouseLeaveEvent.targetDiagram = component.diagram;
    node.mouseLeave(mouseLeaveEvent, node);

    node.ports.each((port) => {
      if (port.portId !== '') {
        expect(port.fill).toBe('transparent');
      }
    });
  });
});






import { ComponentFixture, TestBed, waitForAsync } from '@angular/core/testing';
import { DiagramComponent } from './diagram.component';
import { By } from '@angular/platform-browser';
import * as go from 'gojs';

describe('DiagramComponent', () => {
  let component: DiagramComponent;
  let fixture: ComponentFixture<DiagramComponent>;
  let diagramDiv: HTMLElement;

  beforeEach(waitForAsync(() => {
    TestBed.configureTestingModule({
      declarations: [DiagramComponent],
    }).compileComponents();
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(DiagramComponent);
    component = fixture.componentInstance;
    diagramDiv = fixture.debugElement.query(By.css('div')).nativeElement;
    fixture.detectChanges();
    component.initializeDiagram(diagramDiv);
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should save the diagram', () => {
    spyOn(component, 'save').and.callThrough();
    const saveButton = fixture.debugElement.query(By.css('button')).nativeElement;
    saveButton.click();
    expect(component.save).toHaveBeenCalled();
  });

  it('should load the diagram', () => {
    spyOn(component, 'load').and.callThrough();
    const loadButton = fixture.debugElement.queryAll(By.css('button'))[1].nativeElement;
    loadButton.click();
    expect(component.load).toHaveBeenCalled();
  });

  it('should handle file input correctly', () => {
    spyOn(window, 'alert');
    const input = document.createElement('input');
    input.type = 'file';
    document.body.appendChild(input);

    const event = new Event('change');
    Object.defineProperty(event, 'target', {
      value: { files: [new Blob(['{}'], { type: 'application/json' })] },
    });

    input.dispatchEvent(event);
    component.load();
    expect(window.alert).not.toHaveBeenCalled();
    document.body.removeChild(input);
  });

  it('should handle no file selected', () => {
    spyOn(window, 'alert');
    const input = document.createElement('input');
    input.type = 'file';
    document.body.appendChild(input);

    const event = new Event('change');
    Object.defineProperty(event, 'target', {
      value: { files: [] },
    });

    input.dispatchEvent(event);
    component.load();
    expect(window.alert).toHaveBeenCalledWith('No file selected.');
    document.body.removeChild(input);
  });

  it('should handle multiple files selected', () => {
    spyOn(window, 'alert');
    const input = document.createElement('input');
    input.type = 'file';
    document.body.appendChild(input);

    const event = new Event('change');
    Object.defineProperty(event, 'target', {
      value: { files: [new Blob(['{}'], { type: 'application/json' }), new Blob(['{}'], { type: 'application/json' })] },
    });

    input.dispatchEvent(event);
    component.load();
    expect(window.alert).toHaveBeenCalledWith('Please select a single JSON file.');
    document.body.removeChild(input);
  });

  it('should handle non-JSON file selected', () => {
    spyOn(window, 'alert');
    const input = document.createElement('input');
    input.type = 'file';
    document.body.appendChild(input);

    const event = new Event('change');
    Object.defineProperty(event, 'target', {
      value: { files: [new Blob(['text'], { type: 'text/plain' })] },
    });

    input.dispatchEvent(event);
    component.load();
    expect(window.alert).toHaveBeenCalledWith('Please select a valid JSON file.');
    document.body.removeChild(input);
  });

  it('should show and hide ports on mouse enter/leave', () => {
    const nodeData = { key: 1, text: 'Node 1', loc: '0 0' };
    component.diagram.model = new go.GraphLinksModel([nodeData]);
    const node = component.diagram.findNodeForKey(1);

    // Simulate mouse enter
    component.diagram.toolManager.doMouseEnter(new go.InputEvent(), node);
    node.ports.each((port) => {
      if (port.portId !== '') {
        expect(port.fill).toBe('rgba(0,0,0,.3)');
      }
    });

    // Simulate mouse leave
    component.diagram.toolManager.doMouseLeave(new go.InputEvent(), node);
    node.ports.each((port) => {
      if (port.portId !== '') {
        expect(port.fill).toBe('transparent');
      }
    });
  });
});





// src/app/diagram/diagram.component.spec.ts

import { ComponentFixture, TestBed } from '@angular/core/testing';
import { DiagramComponent } from './diagram.component';
import * as go from 'gojs';

describe('DiagramComponent', () => {
  let component: DiagramComponent;
  let fixture: ComponentFixture<DiagramComponent>;
  let saveButton: HTMLButtonElement;
  let loadButton: HTMLButtonElement;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [DiagramComponent]
    }).compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(DiagramComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
    saveButton = fixture.nativeElement.querySelector('button[saveButton]');
    loadButton = fixture.nativeElement.querySelector('button[loadButton]');
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should save the diagram model', () => {
    spyOn(component, 'save').and.callThrough();
    saveButton.click();
    expect(component.save).toHaveBeenCalled();
  });

  it('should load a diagram model from a valid JSON file', () => {
    spyOn(component, 'load').and.callThrough();
    spyOn(window, 'alert');

    const input = document.createElement('input');
    spyOn(document, 'createElement').and.returnValue(input);

    loadButton.click();
    expect(component.load).toHaveBeenCalled();

    // Mock File input
    const file = new Blob(['{}'], { type: 'application/json' });
    Object.defineProperty(input, 'files', {
      value: [file],
    });

    // Trigger file input change
    const event = new Event('change');
    input.dispatchEvent(event);

    expect(window.alert).not.toHaveBeenCalled();
  });

  it('should alert if multiple files are selected', () => {
    spyOn(component, 'load').and.callThrough();
    spyOn(window, 'alert');

    const input = document.createElement('input');
    spyOn(document, 'createElement').and.returnValue(input);

    loadButton.click();
    expect(component.load).toHaveBeenCalled();

    // Mock multiple files input
    const file1 = new Blob(['{}'], { type: 'application/json' });
    const file2 = new Blob(['{}'], { type: 'application/json' });
    Object.defineProperty(input, 'files', {
      value: [file1, file2],
    });

    // Trigger file input change
    const event = new Event('change');
    input.dispatchEvent(event);

    expect(window.alert).toHaveBeenCalledWith('Please select a single JSON file.');
  });

  it('should alert if a non-JSON file is selected', () => {
    spyOn(component, 'load').and.callThrough();
    spyOn(window, 'alert');

    const input = document.createElement('input');
    spyOn(document, 'createElement').and.returnValue(input);

    loadButton.click();
    expect(component.load).toHaveBeenCalled();

    // Mock non-JSON file input
    const file = new Blob([''], { type: 'text/plain' });
    Object.defineProperty(input, 'files', {
      value: [file],
    });

    // Trigger file input change
    const event = new Event('change');
    input.dispatchEvent(event);

    expect(window.alert).toHaveBeenCalledWith('Please select a valid JSON file.');
  });

  it('should alert if there is an error loading the JSON file', () => {
    spyOn(component, 'load').and.callThrough();
    spyOn(window, 'alert');

    const input = document.createElement('input');
    spyOn(document, 'createElement').and.returnValue(input);

    loadButton.click();
    expect(component.load).toHaveBeenCalled();

    // Mock JSON file with invalid content
    const file = new Blob(['{invalid-json}'], { type: 'application/json' });
    Object.defineProperty(input, 'files', {
      value: [file],
    });

    // Trigger file input change
    const event = new Event('change');
    input.dispatchEvent(event);

    // Force the FileReader to call the onload handler with invalid JSON
    const reader = new FileReader();
    spyOn(window, 'FileReader').and.returnValue({
      readAsText: () => {
        reader.onload!({ target: { result: '{invalid-json}' } });
      },
      set onload(handler) {
        reader.onload = handler;
      },
      get onload() {
        return reader.onload;
      },
      set onerror(handler) {
        reader.onerror = handler;
      },
      get onerror() {
        return reader.onerror;
      }
    } as unknown as FileReader);

    input.dispatchEvent(event);

    expect(window.alert).toHaveBeenCalledWith('Error loading JSON file.');
  });
});






// src/app/diagram/diagram.component.ts

import { Component, ElementRef, ViewChild, AfterViewInit } from '@angular/core';
import * as go from 'gojs';
import { initDiagram } from '../gojs-inits';

@Component({
  selector: 'app-diagram',
  template: `
    <button #saveButton [disabled]="!isModified" (click)="save()">Save</button>
    <button #loadButton (click)="load()">Load</button>
    <div #diagramDiv style="width:600px; height:400px; border:1px solid black"></div>
  `,
  styles: []
})
export class DiagramComponent implements AfterViewInit {
  @ViewChild('saveButton', { static: true }) saveButton: ElementRef<HTMLButtonElement>;
  @ViewChild('loadButton', { static: true }) loadButton: ElementRef<HTMLButtonElement>;
  @ViewChild('diagramDiv', { static: true }) diagramDiv: ElementRef<HTMLDivElement>;
  diagram: go.Diagram;
  isModified: boolean = false;

  constructor() {
    this.onDiagramModified = this.onDiagramModified.bind(this);
  }

  ngAfterViewInit() {
    this.initializeDiagram(this.diagramDiv.nativeElement);
  }

  initializeDiagram(diagramDiv: HTMLDivElement): void {
    this.diagram = initDiagram();
    this.diagram.div = diagramDiv;
    this.diagram.addDiagramListener('Modified', this.onDiagramModified);
  }

  onDiagramModified(e: go.DiagramEvent) {
    this.isModified = this.diagram.isModified;
    const idx = document.title.indexOf('*');
    if (this.diagram.isModified) {
      if (idx < 0) document.title += '*';
    } else {
      if (idx >= 0) document.title = document.title.slice(0, idx);
    }
  }

  save() {
    const modelData = this.diagram.model.toJson();
    const blob = new Blob([modelData], { type: 'application/json' });
    const url = URL.createObjectURL(blob);

    const a = document.createElement('a');
    a.href = url;
    a.download = 'diagram.json';
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
    URL.revokeObjectURL(url);

    this.diagram.isModified = false;
    this.onDiagramModified(null); // Call the handler manually to update the state
  }

  load() {
    const input = document.createElement('input');
    input.type = 'file';
    input.accept = 'application/json';
    input.onchange = (event) => {
      const files = (event.target as HTMLInputElement).files;
      if (!files || files.length !== 1) {
        alert('Please select a single JSON file.');
        return;
      }

      const file = files[0];
      if (!file.name.endsWith('.json')) {
        alert('Please select a valid JSON file.');
        return;
      }

      const reader = new FileReader();
      reader.onload = (evt) => {
        try {
          const json = evt.target?.result as string;
          this.diagram.model = go.Model.fromJson(json);
          this.diagram.isModified = false;
          this.onDiagramModified(null); // Call the handler manually to update the state
        } catch (error) {
          alert('Error loading JSON file.');
        }
      };
      reader.onerror = () => {
        alert('Error reading file.');
      };
      reader.readAsText(file);
    };
    input.click();
  }
}











// src/app/diagram/diagram.component.spec.ts

import { ComponentFixture, TestBed } from '@angular/core/testing';
import { DiagramComponent } from './diagram.component';
import * as go from 'gojs';

describe('DiagramComponent', () => {
  let component: DiagramComponent;
  let fixture: ComponentFixture<DiagramComponent>;
  let saveButton: HTMLButtonElement;
  let loadButton: HTMLButtonElement;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [DiagramComponent]
    }).compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(DiagramComponent);
    component = fixture.componentInstance;
    saveButton = fixture.nativeElement.querySelector('button[saveButton]');
    loadButton = fixture.nativeElement.querySelector('button[loadButton]');
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should save the diagram model', () => {
    spyOn(component, 'save').and.callThrough();
    saveButton.click();
    expect(component.save).toHaveBeenCalled();
  });

  it('should load a diagram model from a valid JSON file', () => {
    spyOn(component, 'load').and.callThrough();
    spyOn(window, 'alert');

    const input = document.createElement('input');
    spyOn(document, 'createElement').and.returnValue(input);

    loadButton.click();
    expect(component.load).toHaveBeenCalled();

    // Mock File input
    const file = new Blob(['{}'], { type: 'application/json' });
    Object.defineProperty(input, 'files', {
      value: [file],
    });

    // Trigger file input change
    const event = new Event('change');
    input.dispatchEvent(event);

    expect(window.alert).not.toHaveBeenCalled();
  });

  it('should alert if multiple files are selected', () => {
    spyOn(component, 'load').and.callThrough();
    spyOn(window, 'alert');

    const input = document.createElement('input');
    spyOn(document, 'createElement').and.returnValue(input);

    loadButton.click();
    expect(component.load).toHaveBeenCalled();

    // Mock multiple files input
    const file1 = new Blob(['{}'], { type: 'application/json' });
    const file2 = new Blob(['{}'], { type: 'application/json' });
    Object.defineProperty(input, 'files', {
      value: [file1, file2],
    });

    // Trigger file input change
    const event = new Event('change');
    input.dispatchEvent(event);

    expect(window.alert).toHaveBeenCalledWith('Please select a single JSON file.');
  });

  it('should alert if a non-JSON file is selected', () => {
    spyOn(component, 'load').and.callThrough();
    spyOn(window, 'alert');

    const input = document.createElement('input');
    spyOn(document, 'createElement').and.returnValue(input);

    loadButton.click();
    expect(component.load).toHaveBeenCalled();

    // Mock non-JSON file input
    const file = new Blob([''], { type: 'text/plain' });
    Object.defineProperty(input, 'files', {
      value: [file],
    });

    // Trigger file input change
    const event = new Event('change');
    input.dispatchEvent(event);

    expect(window.alert).toHaveBeenCalledWith('Please select a valid JSON file.');
  });

  it('should alert if there is an error loading the JSON file', () => {
    spyOn(component, 'load').and.callThrough();
    spyOn(window, 'alert');

    const input = document.createElement('input');
    spyOn(document, 'createElement').and.returnValue(input);

    loadButton.click();
    expect(component.load).toHaveBeenCalled();

    // Mock JSON file with invalid content
    const file = new Blob(['{invalid-json}'], { type: 'application/json' });
    Object.defineProperty(input, 'files', {
      value: [file],
    });

    // Trigger file input change
    const event = new Event('change');
    input.dispatchEvent(event);

    expect(window.alert).toHaveBeenCalledWith('Error loading JSON file.');
  });
});
