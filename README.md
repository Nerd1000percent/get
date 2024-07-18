import { Injectable } from '@angular/core';
import * as go from 'gojs';

@Injectable({
  providedIn: 'root'
})
export class DiagramService {
  private diagram: go.Diagram;

  constructor() {}

  initDiagram(): void {
    const $ = go.GraphObject.make;

    this.diagram = $(go.Diagram, 'myDiagramDiv', {
      'undoManager.isEnabled': true
    });

    this.diagram.nodeTemplate = $(go.Node, 'Auto',
      $(go.Shape, 'Rectangle', { fill: 'white' },
        new go.Binding('figure', 'figure')),
      $(go.TextBlock, { margin: 8 },
        new go.Binding('text', 'key'))
    );

    this.diagram.nodeTemplateMap.add('Diamond',
      $(go.Node, 'Auto',
        $(go.Shape, 'Diamond', { fill: 'lightyellow' },
          new go.Binding('figure', 'figure')),
        $(go.TextBlock, { margin: 8 },
          new go.Binding('text', 'key'))
      ));

    this.diagram.groupTemplate = $(go.Group, 'Vertical', {
        layout: $(go.GridLayout),
        computesBoundsAfterDrag: true,
        handlesDragDropForMembers: true,
        isSubGraphExpanded: false // start with groups collapsed
      },
      $(go.Panel, 'Auto',
        $(go.Shape, 'Rectangle', { fill: 'lightblue' }),
        $(go.TextBlock, { margin: 8 },
          new go.Binding('text', 'key'))
      ),
      $(go.Placeholder, { padding: 10 })
    );

    this.diagram.model = new go.GraphLinksModel([
      { key: 'Alpha', figure: 'Circle' },
      { key: 'Beta' },
      { key: 'Gamma' },
      { key: 'Diamond1', figure: 'Diamond' },
      { key: 'Group1', isGroup: true }
    ], [
      { from: 'Alpha', to: 'Group1' },
      { from: 'Beta', to: 'Group1' },
      { from: 'Gamma', to: 'Group1' }
    ]);
  }

  getDiagram(): go.Diagram {
    return this.diagram;
  }

  validateDiagram(): boolean {
    const nodes = this.diagram.nodes;

    let isValid = true;

    nodes.each((node) => {
      if (node instanceof go.Group) {
        node.memberParts.each((member) => {
          if (member.data.figure === 'Diamond' && !this.isNodeLinked(member)) {
            alert(`Group "${node.data.key}" contains a diamond figure that is not linked to anything.`);
            isValid = false;
            return false; // Break out of the loop
          }
        });
        if (!isValid) {
          return false; // Break out of the outer loop
        }
      }
    });

    return isValid;
  }

  isNodeLinked(node: go.Part): boolean {
    const links = node.findLinksConnected();
    return links.count > 0;
  }
}





import { Injectable } from '@angular/core';
import * as go from 'gojs';

@Injectable({
  providedIn: 'root'
})
export class DiagramService {
  private diagram: go.Diagram;

  constructor() {}

  initDiagram(): void {
    const $ = go.GraphObject.make;

    this.diagram = $(go.Diagram, 'myDiagramDiv', {
      'undoManager.isEnabled': true
    });

    this.diagram.nodeTemplate = $(go.Node, 'Auto',
      $(go.Shape, 'Rectangle', { fill: 'white' },
        new go.Binding('figure', 'figure')),
      $(go.TextBlock, { margin: 8 },
        new go.Binding('text', 'key'))
    );

    this.diagram.nodeTemplateMap.add('Diamond',
      $(go.Node, 'Auto',
        $(go.Shape, 'Diamond', { fill: 'lightyellow' },
          new go.Binding('figure', 'figure')),
        $(go.TextBlock, { margin: 8 },
          new go.Binding('text', 'key'))
      ));

    this.diagram.groupTemplate = $(go.Group, 'Vertical', {
        layout: $(go.GridLayout),
        computesBoundsAfterDrag: true,
        handlesDragDropForMembers: true,
        isSubGraphExpanded: false // start with groups collapsed
      },
      $(go.Panel, 'Auto',
        $(go.Shape, 'Rectangle', { fill: 'lightblue' }),
        $(go.TextBlock, { margin: 8 },
          new go.Binding('text', 'key'))
      ),
      $(go.Placeholder, { padding: 10 })
    );

    this.diagram.model = new go.GraphLinksModel([
      { key: 'Alpha', figure: 'Circle' },
      { key: 'Beta' },
      { key: 'Gamma' },
      { key: 'Diamond1', figure: 'Diamond' },
      { key: 'Group1', isGroup: true }
    ], [
      { from: 'Alpha', to: 'Group1' },
      { from: 'Beta', to: 'Group1' },
      { from: 'Gamma', to: 'Group1' }
    ]);
  }

  getDiagram(): go.Diagram {
    return this.diagram;
  }

  validateDiagram(): boolean {
    const nodes = this.diagram.nodes;

    nodes.each((node) => {
      if (node instanceof go.Group) {
        node.memberParts.each((member) => {
          if (member.data.figure === 'Diamond' && !this.isNodeLinked(member)) {
            alert(`Group "${node.data.key}" contains a diamond figure that is not linked to anything.`);
            return false;
          }
        });
      }
    });
    return true;
  }

  isNodeLinked(node: go.Part): boolean {
    const links = node.findLinksConnected();
    return links.count > 0;
  }
}




<!DOCTYPE html>
<html>
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <script src="https://unpkg.com/gojs/release/go-debug.js"></script>
  <script id="code">
    function init() {
      const $ = go.GraphObject.make;

      // Initialize the diagram
      const myDiagram = $(go.Diagram, "myDiagramDiv", {
        "undoManager.isEnabled": true
      });

      // Define a simple Node template
      myDiagram.nodeTemplate =
        $(go.Node, "Auto",
          $(go.Shape, "Rectangle", { fill: "white" },
            new go.Binding("figure", "figure")),
          $(go.TextBlock, { margin: 8 },
            new go.Binding("text", "key"))
        );

      // Define the template for diamond nodes
      myDiagram.nodeTemplateMap.add("Diamond",
        $(go.Node, "Auto",
          $(go.Shape, "Diamond", { fill: "lightyellow" },
            new go.Binding("figure", "figure")),
          $(go.TextBlock, { margin: 8 },
            new go.Binding("text", "key"))
        ));

      // Define a simple Group template
      myDiagram.groupTemplate =
        $(go.Group, "Vertical",
          {
            layout: $(go.GridLayout),
            computesBoundsAfterDrag: true,
            handlesDragDropForMembers: true,
            isSubGraphExpanded: false // start with groups collapsed
          },
          $(go.Panel, "Auto",
            $(go.Shape, "Rectangle", { fill: "lightblue" }),
            $(go.TextBlock, { margin: 8 },
              new go.Binding("text", "key"))
          ),
          $(go.Placeholder, { padding: 10 })
        );

      // Define the model
      myDiagram.model = new go.GraphLinksModel(
        [
          { key: "Alpha", figure: "Circle" },
          { key: "Beta" },
          { key: "Gamma" },
          { key: "Diamond1", figure: "Diamond" },
          { key: "Group1", isGroup: true }
        ],
        [
          { from: "Alpha", to: "Group1" },
          { from: "Beta", to: "Group1" },
          { from: "Gamma", to: "Group1" }
        ]
      );

      // Add a save button
      document.getElementById("saveButton").addEventListener("click", function() {
        if (validateDiagram(myDiagram)) {
          const json = myDiagram.model.toJson();
          console.log("Diagram saved:", json);
        }
      });

      // Function to validate the diagram
      function validateDiagram(diagram) {
        const nodes = diagram.nodes;
        
        nodes.each(function(node) {
          if (node instanceof go.Group) {
            node.memberParts.each(function(member) {
              if (member.data.figure === "Diamond" && !isNodeLinked(member)) {
                alert(`Group "${node.data.key}" contains a diamond figure that is not linked to anything.`);
                return false;
              }
            });
          }
        });
        return true;
      }

      // Function to check if a node is linked to anything
      function isNodeLinked(node) {
        const links = node.findLinksConnected();
        return links.count > 0;
      }
    }
  </script>
</head>
<body onload="init()">
  <div id




<!DOCTYPE html>
<html>
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <script src="https://unpkg.com/gojs/release/go-debug.js"></script>
  <script id="code">
    function init() {
      const $ = go.GraphObject.make;

      // Initialize the diagram
      const myDiagram = $(go.Diagram, "myDiagramDiv", {
        "undoManager.isEnabled": true
      });

      // Define a simple Node template
      myDiagram.nodeTemplate =
        $(go.Node, "Auto",
          $(go.Shape, "Rectangle", { fill: "white" },
            new go.Binding("figure", "figure")),
          $(go.TextBlock, { margin: 8 },
            new go.Binding("text", "key"))
        );

      // Define the template for diamond nodes
      myDiagram.nodeTemplateMap.add("Diamond",
        $(go.Node, "Auto",
          $(go.Shape, "Diamond", { fill: "lightyellow" },
            new go.Binding("figure", "figure")),
          $(go.TextBlock, { margin: 8 },
            new go.Binding("text", "key"))
        ));

      // Define a simple Group template
      myDiagram.groupTemplate =
        $(go.Group, "Vertical",
          {
            layout: $(go.GridLayout),
            computesBoundsAfterDrag: true,
            handlesDragDropForMembers: true,
            isSubGraphExpanded: false // start with groups collapsed
          },
          $(go.Panel, "Auto",
            $(go.Shape, "Rectangle", { fill: "lightblue" }),
            $(go.TextBlock, { margin: 8 },
              new go.Binding("text", "key"))
          ),
          $(go.Placeholder, { padding: 10 })
        );

      // Define the model
      myDiagram.model = new go.GraphLinksModel(
        [
          { key: "Alpha", figure: "Circle" },
          { key: "Beta" },
          { key: "Gamma" },
          { key: "Diamond1", figure: "Diamond" },
          { key: "Group1", isGroup: true }
        ],
        [
          { from: "Alpha", to: "Group1" },
          { from: "Beta", to: "Group1" },
          { from: "Gamma", to: "Group1" }
        ]
      );

      // Add a save button
      document.getElementById("saveButton").addEventListener("click", function() {
        if (validateDiagram(myDiagram)) {
          const json = myDiagram.model.toJson();
          console.log("Diagram saved:", json);
        }
      });

      // Function to validate the diagram
      function validateDiagram(diagram) {
        const model = diagram.model;
        const nodeDataArray = model.nodeDataArray;

        for (let i = 0; i < nodeDataArray.length; i++) {
          const data = nodeDataArray[i];
          if (data.isGroup) {
            const groupNode = diagram.findNodeForData(data);
            const memberParts = model.memberParts(data).toArray();
            for (let j = 0; j < memberParts.length; j++) {
              const memberData = memberParts[j].data;
              if (memberData.figure === "Diamond" && !isNodeLinked(memberData.key)) {
                alert(`Group "${data.key}" contains a diamond figure that is not linked to anything.`);
                return false;
              }
            }
          }
        }
        return true;
      }

      // Function to check if a node is linked to anything
      function isNodeLinked(nodeKey) {
        const links = myDiagram.findLinksByExample({ from: nodeKey });
        if (links.count > 0) return true;

        const linksTo = myDiagram.findLinksByExample({ to: nodeKey });
        if (linksTo.count > 0) return true;

        return false;
      }
    }
  </script>
</head>
<body onload="init()">
  <div id="myDiagramDiv" style="width:100%; height:600px; border:1px solid black"></div>
  <button id="saveButton">Save Diagram</button>
</body>
</html>




<!DOCTYPE html>
<html>
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <script src="https://unpkg.com/gojs/release/go-debug.js"></script>
  <script id="code">
    function init() {
      const $ = go.GraphObject.make;

      // Initialize the diagram
      const myDiagram = $(go.Diagram, "myDiagramDiv", {
        "undoManager.isEnabled": true
      });

      // Define a simple Node template
      myDiagram.nodeTemplate =
        $(go.Node, "Auto",
          $(go.Shape, "Rectangle", { fill: "white" },
            new go.Binding("figure", "figure")),
          $(go.TextBlock, { margin: 8 },
            new go.Binding("text", "key"))
        );

      // Define a simple Group template
      myDiagram.groupTemplate =
        $(go.Group, "Vertical",
          {
            layout: $(go.GridLayout),
            computesBoundsAfterDrag: true,
            handlesDragDropForMembers: true,
            isSubGraphExpanded: false, // start with groups collapsed
            subGraphExpandedChanged: function(group) {
              checkForSpecificFigure(group.data.key);
            }
          },
          $(go.Panel, "Auto",
            $(go.Shape, "Rectangle", { fill: "lightblue" }),
            $(go.TextBlock, { margin: 8 },
              new go.Binding("text", "key"))
          ),
          $(go.Placeholder, { padding: 10 })
        );

      // Define the model
      myDiagram.model = new go.GraphLinksModel(
        [
          { key: "Alpha", figure: "Circle" },
          { key: "Beta" },
          { key: "Gamma" },
          { key: "Group1", isGroup: true }
        ],
        [
          { from: "Alpha", to: "Group1" },
          { from: "Beta", to: "Group1" },
          { from: "Gamma", to: "Group1" }
        ]
      );

      // Check for specific figure in group members using model data
      function checkForSpecificFigure(groupKey) {
        const model = myDiagram.model;
        const nodeDataArray = model.nodeDataArray;
        const groupData = model.findNodeDataForKey(groupKey);

        if (groupData && groupData.isGroup) {
          const memberKeys = model.memberParts(groupData).toArray().map(member => member.key);
          const containsSpecificFigure = memberKeys.some(key => {
            const nodeData = model.findNodeDataForKey(key);
            return nodeData && nodeData.figure === "Circle";
          });

          if (containsSpecificFigure) {
            alert(`Group "${groupKey}" contains a member with a "Circle" figure.`);
          }
        }
      }
    }
  </script>
</head>
<body onload="init()">
  <div id="myDiagramDiv" style="width:100%; height:600px; border:1px solid black"></div>
</body>
</html>




<!DOCTYPE html>
<html>
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <script src="https://unpkg.com/gojs/release/go-debug.js"></script>
  <script id="code">
    function init() {
      const $ = go.GraphObject.make;

      // Initialize the diagram
      const myDiagram = $(go.Diagram, "myDiagramDiv", {
        "undoManager.isEnabled": true
      });

      // Define a simple Node template
      myDiagram.nodeTemplate =
        $(go.Node, "Auto",
          $(go.Shape, "Rectangle", { fill: "white" },
            new go.Binding("figure", "figure")),
          $(go.TextBlock, { margin: 8 },
            new go.Binding("text", "key"))
        );

      // Define a simple Group template
      myDiagram.groupTemplate =
        $(go.Group, "Vertical",
          {
            layout: $(go.GridLayout),
            computesBoundsAfterDrag: true,
            handlesDragDropForMembers: true,
            isSubGraphExpanded: false, // start with groups collapsed
            subGraphExpandedChanged: checkForSpecificFigure
          },
          $(go.Panel, "Auto",
            $(go.Shape, "Rectangle", { fill: "lightblue" }),
            $(go.TextBlock, { margin: 8 },
              new go.Binding("text", "key"))
          ),
          $(go.Placeholder, { padding: 10 })
        );

      // Define the model
      myDiagram.model = new go.GraphLinksModel(
        [
          { key: "Alpha", figure: "Circle" },
          { key: "Beta" },
          { key: "Gamma" },
          { key: "Group1", isGroup: true }
        ],
        [
          { from: "Alpha", to: "Group1" },
          { from: "Beta", to: "Group1" },
          { from: "Gamma", to: "Group1" }
        ]
      );

      // Check for specific figure in group members
      function checkForSpecificFigure(group) {
        group.memberParts.each(function(part) {
          if (part instanceof go.Node && part.data.figure === "Circle") {
            alert(`Group "${group.data.key}" contains a member with a "Circle" figure.`);
          }
        });
      }
    }
  </script>
</head>
<body onload="init()">
  <div id="myDiagramDiv" style="width:100%; height:600px; border:1px solid black"></div>
</body>
</html>




import * as go from 'gojs';

// Define the button creation function with types
function makeButton(
  text: string,
  action: (e: go.InputEvent, obj: go.GraphObject) => void,
  visiblePredicate?: (obj: go.GraphObject) => boolean
): go.GraphObject {
  return go.GraphObject.make('ContextMenuButton',
    go.GraphObject.make(go.TextBlock, text),
    { click: action },
    // don't bother with binding GraphObject.visible if there's no predicate
    visiblePredicate ? new go.Binding('visible', '', (obj, e) => (obj.diagram ? visiblePredicate(obj) : false)).ofObject() : {}
  );
}

// Context menu setup
const myDiagram = go.GraphObject.make(go.Diagram, 'myDiagramDiv');

myDiagram.contextMenu = go.GraphObject.make('ContextMenu',
  makeButton(
    'Paste',
    (e: go.InputEvent, obj: go.GraphObject) => e.diagram.commandHandler.pasteSelection(e.diagram.toolManager.contextMenuTool.mouseDownPoint),
    (obj: go.GraphObject) => obj.diagram.commandHandler.canPasteSelection(obj.diagram.toolManager.contextMenuTool.mouseDownPoint)
  ),
  makeButton(
    'Undo',
    (e: go.InputEvent, obj: go.GraphObject) => e.diagram.commandHandler.undo(),
    (obj: go.GraphObject) => obj.diagram.commandHandler.canUndo()
  ),
  makeButton(
    'Redo',
    (e: go.InputEvent, obj: go.GraphObject) => e.diagram.commandHandler.redo(),
    (obj: go.GraphObject) => obj.diagram.commandHandler.canRedo()
  )
);





import * as go from 'gojs';

// Define the button creation function with types
function makeButton(
  text: string,
  action: (e: go.InputEvent, obj: go.GraphObject) => void,
  visiblePredicate: (obj: go.GraphObject) => boolean,
  options?: { isActionable: boolean }
): go.GraphObject {
  return go.GraphObject.make(go.TextBlock, text,
    {
      margin: 2,
      click: action,
      visible: visiblePredicate,
      isActionable: options?.isActionable ?? false // Use optional chaining and nullish coalescing
    }
  );
}

// Context menu setup
const myDiagram = go.GraphObject.make(go.Diagram, 'myDiagramDiv');

myDiagram.contextMenu = go.GraphObject.make(go.Adornment, 'ContextMenu',
  makeButton(
    'Paste',
    (e: go.InputEvent, obj: go.GraphObject) => e.diagram.commandHandler.pasteSelection(e.diagram.toolManager.contextMenuTool.mouseDownPoint),
    (obj: go.GraphObject) => obj.diagram.commandHandler.canPasteSelection(obj.diagram.toolManager.contextMenuTool.mouseDownPoint),
    { isActionable: true }
  ),
  makeButton(
    'Undo',
    (e: go.InputEvent, obj: go.GraphObject) => e.diagram.commandHandler.undo(),
    (obj: go.GraphObject) => obj.diagram.commandHandler.canUndo(),
    { isActionable: true }
  ),
  makeButton(
    'Redo',
    (e: go.InputEvent, obj: go.GraphObject) => e.diagram.commandHandler.redo(),
    (obj: go.GraphObject) => obj.diagram.commandHandler.canRedo(),
    { isActionable: true }
  )
);




import { ComponentFixture, TestBed } from '@angular/core/testing';
import { GojsDiagramComponent } from './gojs-diagram.component';
import * as go from 'gojs';
import { initDiagram } from './path-to-your-file';

describe('GojsDiagramComponent', () => {
  let component: GojsDiagramComponent;
  let fixture: ComponentFixture<GojsDiagramComponent>;
  let diagram: go.Diagram;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [GojsDiagramComponent],
    }).compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(GojsDiagramComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
    diagram = initDiagram();
    diagram.div = fixture.nativeElement.querySelector('div');
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should call finishDrop on mouse drop', (done) => {
    const event = new go.InputEvent();
    event.diagram = diagram;
    
    spyOn(diagram.commandHandler, 'addTopLevelParts').and.returnValue(true);
    spyOn(diagram.currentTool, 'doCancel');

    const finishDrop = diagram.toolManager.mouseDrop;
    finishDrop(event);

    fixture.whenStable().then(() => {
      expect(diagram.commandHandler.addTopLevelParts).toHaveBeenCalled();
      expect(diagram.currentTool.doCancel).not.toHaveBeenCalled();
      done();
    });
  });

  it('should calculate correct group depth', () => {
    const group1 = new go.Group();
    const group2 = new go.Group();
    const node = new go.Node();
    group1.add(group2);
    group2.add(node);

    diagram.add(group1);

    const depth = groupDepth(group1);
    expect(depth).toBe(3);
  });

  it('should update total group depth', () => {
    const group1 = new go.Group();
    const group2 = new go.Group();
    const node = new go.Node();
    group1.add(group2);
    group2.add(node);

    diagram.add(group1);

    spyOn(window, 'groupDepth').and.callThrough();
    updateTotalGroupDepth();

    expect(groupDepth).toHaveBeenCalled();
  });
});




import go from 'gojs';
import { groupTemplate, nodeTemplate } from '../gojs/gojs-node-template';
const $ = go.GraphObject.make;

// initializes the diagram template
export function initDiagram() {
  const diagram: go.Diagram = $(go.Diagram, {
    'draggingTool.dragsLink': true,
    InitialLayoutCompleted: () => updateTotalGroupDepth(),
    // when a drag-drop occurs in the Diagram's background, make it a top-level node
    mouseDrop: (e: go.InputEvent) => finishDrop(e),

    //ModelChanged: (e: go.ChangedEvent) => this.onDiagramModelChanged(e),
    'draggingTool.isGridSnapEnabled': true,
    hasHorizontalScrollbar: true,
    'linkingTool.isUnconnectedLinkValid': true,
    'linkingTool.portGravity': 20,
    'relinkingTool.isUnconnectedLinkValid': true,
    'relinkingTool.portGravity': 20,
    'linkingTool.linkValidation': validateLink,
    'relinkingTool.linkValidation': validateLink,
    'toolManager.mouseWheelBehavior': go.WheelMode.Zoom,

    'relinkingTool.fromHandleArchetype': $(go.Shape, 'Diamond', {
      segmentIndex: 0,
      cursor: 'pointer',
      desiredSize: new go.Size(8, 8),
      fill: 'tomato',
      stroke: 'darkred',
    }),
    'relinkingTool.toHandleArchetype': $(go.Shape, 'Diamond', {
      segmentIndex: -1,
      cursor: 'pointer',
      desiredSize: new go.Size(8, 8),
      fill: 'darkred',
      stroke: 'tomato',
    }),
    'linkReshapingTool.handleArchetype': $(go.Shape, 'Diamond', {
      desiredSize: new go.Size(7, 7),
      fill: 'lightblue',
      stroke: 'deepskyblue',
    }),
    'rotatingTool.handleAngle': 270,
    'rotatingTool.handleDistance': 30,
    'rotatingTool.snapAngleMultiple': 15,
    'rotatingTool.snapAngleEpsilon': 15,
    'undoManager.isEnabled': true,

    'clickCreatingTool.archetypeNodeData': {
      text: 'new node',
      color: 'purple',
    },

    model: $(go.GraphLinksModel, {
      nodeKeyProperty: 'id',
      linkToPortIdProperty: 'toPort',
      linkFromPortIdProperty: 'fromPort',
      linkKeyProperty: 'key', // IMPORTANT! must be defined for merges and data sync when using GraphLinksModel
    }),
    grid: $(
      go.Panel,
      'Grid',
      $(go.Shape, 'LineH', { stroke: 'lightgray', strokeWidth: 0.5 }),
      $(go.Shape, 'LineH', {
        stroke: 'gray',
        strokeWidth: 0.5,
        interval: 10,
      }),
      $(go.Shape, 'LineV', { stroke: 'lightgray', strokeWidth: 0.5 }),
      $(go.Shape, 'LineV', {
        stroke: 'gray',
        strokeWidth: 0.5,
        interval: 10,
      }),
    ),
  });
  diagram.nodeTemplate = nodeTemplate;
  diagram.add(
    $(
      go.Part, // this Part is not bound to any model data
      {
        layerName: 'Background',
        position: new go.Point(0, 0),
        selectable: false,
        pickable: false,
      },
      $(
        go.Picture,
        // enable to place a picture on the diagram  'https://miro.medium.com/v2/format:webp/0*ZjYSm_q36J4KChdn',
      ),
    ),
  );

  const linkSelectionAdornmentTemplate = $(
    go.Adornment,
    'Link',
    $(
      go.Shape,
      // isPanelMain declares that this Shape shares the Link.geometry
      {
        isPanelMain: true,
        fill: null,
        stroke: 'deepskyblue',
        strokeWidth: 0,
      },
    ), // use selection object's strokeWidth
  );
  diagram.linkTemplate = $(
    go.Link, // the whole link panel
    {
      selectable: true,
      selectionAdornmentTemplate: linkSelectionAdornmentTemplate,
    },
    { relinkableFrom: true, relinkableTo: true, reshapable: true },
    {
      routing: go.Routing.AvoidsNodes,
      curve: go.Curve.JumpOver,
      corner: 5,
      toShortLength: 4,
    },
    new go.Binding('points').makeTwoWay(),
    $(
      go.Shape, // the link path shape
      { isPanelMain: true, strokeWidth: 2 },
    ),
    $(
      go.Shape, // the arrowhead
      { toArrow: 'Standard', stroke: null },
    ),
    $(
      go.Panel,
      'Auto',
      new go.Binding('visible', 'isSelected').ofObject(),
      $(
        go.Shape,
        'RoundedRectangle', // the link shape
        { fill: '#F8F8F8', stroke: null },
      ),
      $(
        go.TextBlock,
        {
          textAlign: 'center',
          font: '9pt helvetica, arial, sans-serif',
          stroke: '#919191',
          margin: 2,
          minSize: new go.Size(10, NaN),
          editable: true,
        },
        new go.Binding('text').makeTwoWay(),
      ),
    ),
    $(
      go.Shape, // the arrowhead
      { toArrow: 'Standard', stroke: null, visible: false },
    ),
  );
  diagram.groupTemplate = groupTemplate;
  // end Auto Panel

  diagram.nodeTemplateMap.add(
    'Highlighted',
    $(
      go.Node,
      'Auto',
      { locationSpot: go.Spot.Center },
      $(go.Shape, 'Rectangle', {
        fill: null,
        stroke: 'dodgerblue',
        strokeWidth: 2,
      }),
      $(go.Placeholder),
    ),
  );
  (diagram.toolManager.linkingTool.linkValidation = validateLink),
    (diagram.toolManager.relinkingTool.linkValidation = validateLink),
    diagram.addDiagramListener('LinkDrawn', function (e) {
      updatedLinkStyle(e.diagram);
    });
  diagram.addDiagramListener('LinkRelinked', function (e) {
    updatedLinkStyle(e.diagram);
  });
  function updateTotalGroupDepth() {
    let d = 0;
    diagram.findTopLevelGroups().each((g) => (d = Math.max(d, groupDepth(g))));
  }
  function groupDepth(g: go.GraphObject) {
    if (!(g instanceof go.Group)) return 0;
    let d = 1;
    g.memberParts.each((m) => (d = Math.max(d, groupDepth(m) + 1)));
    return d;
  }
  function finishDrop(e: go.InputEvent) {
    const ok = e.diagram.commandHandler.addTopLevelParts(
      e.diagram.selection,
      true,
    );
    if (!ok) e.diagram.currentTool.doCancel();
    updateTotalGroupDepth();
  }
  updatedLinkStyle(diagram); //initial update to set the links style.
  return diagram;
}
export function updatedLinkStyle(diagram: go.Diagram) {
  diagram.links.each(function (link) {
    const fromNode = link.fromNode;
    const toNode = link.toNode;
    if (
      (fromNode &&
        toNode &&
        fromNode.data?.category === 'DashedDiamond' &&
        toNode.data?.category === 'DashedCircle') ||
      (fromNode &&
        toNode &&
        fromNode.data?.category === 'DashedCircle' &&
        toNode.data?.category === 'DashedDiamond')
    ) {
      link.path!.strokeDashArray = [4, 4];
      link.elt(1).visible = false;
    } else {
      link.path!.strokeDashArray = null;
      link.elt(1).visible = true;
    }
  });
}
export function validateLink(
  fromNode: go.Node,
  fromPort: go.GraphObject,
  toNode: go.Node,
  toPort: go.GraphObject,
): boolean {
  const fromFigure = fromNode?.data.figure;
  const toFigure = toNode?.data.figure;
  if (
    fromFigure &&
    fromPort &&
    fromFigure == 'Ellipse' &&
    ((toFigure && toPort && toFigure == 'Ellipse') ||
      toFigure === 'RoundedRectangle')
  ) {
    return true;
  } else if (
    (fromFigure === 'RoundedRectangle' && toFigure === 'RoundedRectangle') ||
    toFigure === 'Ellipse'
  ) {
    return true;
  } else if (
    (fromFigure === 'Diamond' && toFigure == 'Ellipse') ||
    (fromFigure == 'Ellipse' && toFigure == 'Diamond')
  ) {
    return true;
  }
  return false;
}







// src/app/gojs-diagram.spec.ts
import * as go from 'gojs';
import { initializeDiagram } from './gojs-diagram';

describe('GoJS Diagram Functions', () => {
  let div: HTMLDivElement;
  let diagram: go.Diagram;

  beforeEach(() => {
    div = document.createElement('div');
    document.body.appendChild(div);
    diagram = initializeDiagram(div);
  });

  afterEach(() => {
    document.body.removeChild(div);
  });

  describe('initializeDiagram', () => {
    it('should initialize a diagram with the specified div', () => {
      expect(diagram).toBeTruthy();
      expect(diagram.div).toBe(div);
    });

    it('should attach finishDrop to ExternalObjectsDropped event', () => {
      spyOn(diagram, 'addDiagramListener').and.callThrough();
      initializeDiagram(div);
      expect(diagram.addDiagramListener).toHaveBeenCalledWith("ExternalObjectsDropped", jasmine.any(Function));
    });
  });

  describe('groupDepth', () => {
    it('should return 0 for non-group objects', () => {
      const node = new go.Node();
      const depth = (diagram as any).groupDepth(node);
      expect(depth).toBe(0);
    });

    it('should return 1 for a group with no members', () => {
      const group = new go.Group();
      diagram.add(group);
      const depth = (diagram as any).groupDepth(group);
      expect(depth).toBe(1);
    });

    it('should return correct depth for nested groups', () => {
      const group1 = new go.Group();
      const group2 = new go.Group();
      const group3 = new go.Group();
      group1.add(group2);
      group2.add(group3);
      diagram.add(group1);

      const depth = (diagram as any).groupDepth(group1);
      expect(depth).toBe(3);
    });

    it('should return correct depth for mixed group and nodes', () => {
      const group1 = new go.Group();
      const group2 = new go.Group();
      const node1 = new go.Node();
      const node2 = new go.Node();
      group1.add(group2);
      group2.add(node1);
      group2.add(node2);
      diagram.add(group1);

      const depth = (diagram as any).groupDepth(group1);
      expect(depth).toBe(2);
    });
  });

  describe('finishDrop', () => {
    let inputEvent: go.InputEvent;
    let commandHandler: jasmine.SpyObj<go.CommandHandler>;
    let currentTool: jasmine.SpyObj<go.Tool>;

    beforeEach(() => {
      commandHandler = jasmine.createSpyObj('CommandHandler', ['addTopLevelParts']);
      currentTool = jasmine.createSpyObj('Tool', ['doCancel']);
      diagram.commandHandler = commandHandler;
      diagram.currentTool = currentTool;
      inputEvent = new go.InputEvent();
      inputEvent.diagram = diagram;
    });

    it('should add top-level parts and not cancel tool if successful', () => {
      commandHandler.addTopLevelParts.and.returnValue(true);

      const finishDropFunction = (diagram as any).finishDrop;
      finishDropFunction(inputEvent);

      expect(commandHandler.addTopLevelParts).toHaveBeenCalledWith(diagram.selection, true);
      expect(currentTool.doCancel).not.toHaveBeenCalled();
    });

    it('should cancel tool if adding top-level parts fails', () => {
      commandHandler.addTopLevelParts.and.returnValue(false);

      const finishDropFunction = (diagram as any).finishDrop;
      finishDropFunction(inputEvent);

      expect(commandHandler.addTopLevelParts).toHaveBeenCalledWith(diagram.selection, true);
      expect(currentTool.doCancel).toHaveBeenCalled();
    });

    it('should call updateTotalGroupDepth after dropping parts', () => {
      spyOn(window, 'updateTotalGroupDepth').and.callFake(() => {});

      const finishDropFunction = (diagram as any).finishDrop;
      finishDropFunction(inputEvent);

      expect(window.updateTotalGroupDepth).toHaveBeenCalled();
    });
  });
});





// src/app/gojs-functions.spec.ts
import * as go from 'gojs';
import { groupDepth, finishDrop, updateTotalGroupDepth } from './gojs-functions';

describe('GoJS Functions', () => {
  describe('groupDepth', () => {
    it('should return 0 for non-group objects', () => {
      const node = new go.Node();
      expect(groupDepth(node)).toBe(0);
    });

    it('should return 1 for a group with no members', () => {
      const group = new go.Group();
      expect(groupDepth(group)).toBe(1);
    });

    it('should return correct depth for nested groups', () => {
      const group1 = new go.Group();
      const group2 = new go.Group();
      const group3 = new go.Group();
      group1.add(group2);
      group2.add(group3);

      expect(groupDepth(group1)).toBe(3);
    });

    it('should return correct depth for mixed group and nodes', () => {
      const group1 = new go.Group();
      const group2 = new go.Group();
      const node1 = new go.Node();
      const node2 = new go.Node();
      group1.add(group2);
      group2.add(node1);
      group2.add(node2);

      expect(groupDepth(group1)).toBe(2);
    });
  });

  describe('finishDrop', () => {
    let diagram: go.Diagram;
    let inputEvent: go.InputEvent;
    let commandHandler: jasmine.SpyObj<go.CommandHandler>;
    let currentTool: jasmine.SpyObj<go.Tool>;

    beforeEach(() => {
      diagram = new go.Diagram();
      commandHandler = jasmine.createSpyObj('CommandHandler', ['addTopLevelParts']);
      currentTool = jasmine.createSpyObj('Tool', ['doCancel']);
      diagram.commandHandler = commandHandler;
      diagram.currentTool = currentTool;
      inputEvent = new go.InputEvent();
      inputEvent.diagram = diagram;
    });

    it('should add top-level parts and not cancel tool if successful', () => {
      commandHandler.addTopLevelParts.and.returnValue(true);

      finishDrop(inputEvent);

      expect(commandHandler.addTopLevelParts).toHaveBeenCalledWith(diagram.selection, true);
      expect(currentTool.doCancel).not.toHaveBeenCalled();
    });

    it('should cancel tool if adding top-level parts fails', () => {
      commandHandler.addTopLevelParts.and.returnValue(false);

      finishDrop(inputEvent);

      expect(commandHandler.addTopLevelParts).toHaveBeenCalledWith(diagram.selection, true);
      expect(currentTool.doCancel).toHaveBeenCalled();
    });

    it('should call updateTotalGroupDepth after dropping parts', () => {
      spyOn(window, 'updateTotalGroupDepth').and.callFake(() => {});

      finishDrop(inputEvent);

      expect(window.updateTotalGroupDepth).toHaveBeenCalled();
    });
  });
});




 function groupDepth(g: go.GraphObject) {
    Iif (!(g instanceof go.Group)) return 0;
    let d = 1;
    g.memberParts.each((m) => (d = Math.max(d, groupDepth(m) + 1)));
    return d;
  }
  function finishDrop(e: go.InputEvent) {
    const ok = e.diagram.commandHandler.addTopLevelParts(
      e.diagram.selection,
      true,
    );
    Iif (!ok) e.diagram.currentTool.doCancel();
    updateTotalGroupDepth();
  }



// src/app/validate-link.spec.ts
import * as go from 'gojs';
import { validateLink } from './validate-link';

describe('validateLink', () => {
  function createNode(figure: string): go.Node {
    return new go.Node().copy({ data: { figure: figure } });
  }

  function createPort(): go.GraphObject {
    return new go.GraphObject();
  }

  it('should return true for valid Ellipse to Ellipse link', () => {
    const fromNode = createNode('Ellipse');
    const toNode = createNode('Ellipse');
    const fromPort = createPort();
    const toPort = createPort();

    const result = validateLink(fromNode, fromPort, toNode, toPort);
    expect(result).toBeTrue();
  });

  it('should return true for valid Ellipse to RoundedRectangle link', () => {
    const fromNode = createNode('Ellipse');
    const toNode = createNode('RoundedRectangle');
    const fromPort = createPort();
    const toPort = createPort();

    const result = validateLink(fromNode, fromPort, toNode, toPort);
    expect(result).toBeTrue();
  });

  it('should return true for valid RoundedRectangle to RoundedRectangle link', () => {
    const fromNode = createNode('RoundedRectangle');
    const toNode = createNode('RoundedRectangle');
    const fromPort = createPort();
    const toPort = createPort();

    const result = validateLink(fromNode, fromPort, toNode, toPort);
    expect(result).toBeTrue();
  });

  it('should return true for valid Diamond to Ellipse link', () => {
    const fromNode = createNode('Diamond');
    const toNode = createNode('Ellipse');
    const fromPort = createPort();
    const toPort = createPort();

    const result = validateLink(fromNode, fromPort, toNode, toPort);
    expect(result).toBeTrue();
  });

  it('should return false for invalid link', () => {
    const fromNode = createNode('Diamond');
    const toNode = createNode('RoundedRectangle');
    const fromPort = createPort();
    const toPort = createPort();

    const result = validateLink(fromNode, fromPort, toNode, toPort);
    expect(result).toBeFalse();
  });
});



export function validateLink(
  fromNode: go.Node,
  fromPort: go.GraphObject,
  toNode: go.Node,
  toPort: go.GraphObject,
): boolean {
  const fromFigure = fromNode?.data.figure;
  const toFigure = toNode?.data.figure;
  if (
    fromFigure &&
    fromPort &&
    fromFigure == 'Ellipse' &&
    ((toFigure && toPort && toFigure == 'Ellipse') ||
      toFigure === 'RoundedRectangle')
  ) {
    return true;
  } else if (
    (fromFigure === 'RoundedRectangle' && toFigure === 'RoundedRectangle') ||
    toFigure === 'Ellipse'
  ) {
    return true;
  } else if (
    (fromFigure === 'Diamond' && toFigure == 'Ellipse') ||
    (fromFigure == 'Ellipse' && toFigure == 'Diamond')
  ) {
    return true;
  }
  return false;
}export function validateLink(
  fromNode: go.Node,
  fromPort: go.GraphObject,
  toNode: go.Node,
  toPort: go.GraphObject,
): boolean {
  const fromFigure = fromNode?.data.figure;
  const toFigure = toNode?.data.figure;
  if (
    fromFigure &&
    fromPort &&
    fromFigure == 'Ellipse' &&
    ((toFigure && toPort && toFigure == 'Ellipse') ||
      toFigure === 'RoundedRectangle')
  ) {
    return true;
  } else if (
    (fromFigure === 'RoundedRectangle' && toFigure === 'RoundedRectangle') ||
    toFigure === 'Ellipse'
  ) {
    return true;
  } else if (
    (fromFigure === 'Diamond' && toFigure == 'Ellipse') ||
    (fromFigure == 'Ellipse' && toFigure == 'Diamond')
  ) {
    return true;
  }
  return false;
}
<!DOCTYPE html>
<html>
<head>
  <title>GoJS Group Template with Links and Ports</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/gojs/2.1.36/go.js"></script>
  <style>
    html, body, #myDiagramDiv {
      width: 100%; 
      height: 100%; 
      margin: 0; 
      padding: 0;
    }
  </style>
</head>
<body>
  <div id="myDiagramDiv"></div>
  <script>
    function init() {
      var $ = go.GraphObject.make;

      var myDiagram = $(go.Diagram, "myDiagramDiv",
        {
          "undoManager.isEnabled": true,
          layout: $(go.LayeredDigraphLayout)
        });

      // Define the node template
      myDiagram.nodeTemplate =
        $(go.Node, "Auto",
          $(go.Shape, "RoundedRectangle", { fill: "white" }),
          $(go.TextBlock, { margin: 8 },
            new go.Binding("text", "text"))
        );

      // Define the link template
      myDiagram.linkTemplate =
        $(go.Link,
          { routing: go.Link.AvoidsNodes, corner: 5 },
          $(go.Shape),
          $(go.Shape, { toArrow: "Standard" })
        );

      // Define a function to create a port
      function makePort(name, spot, output, input) {
        return $(go.Shape, "Circle",
          {
            fill: "transparent",
            stroke: null,
            desiredSize: new go.Size(8, 8),
            alignment: spot, alignmentFocus: spot,  // align the port on the main Shape
            portId: name,  // declare this object to be a "port"
            fromSpot: spot, toSpot: spot,  // declare where links may connect at this port
            fromLinkable: output, toLinkable: input,  // declare whether the user may draw links to/from here
            cursor: "pointer"  // show a different cursor to indicate potential link point
          });
      }

      // Define the group template
      myDiagram.groupTemplate =
        $(go.Group, "Auto",
          {
            ungroupable: true,
            // highlight when dragging into the Group
            mouseDragEnter: (e, grp, prev) => highlightGroup(e, grp, true),
            mouseDragLeave: (e, grp, next) => highlightGroup(e, grp, false),
            computesBoundsAfterDrag: true,
            computesBoundsIncludingLocation: true,
            // when the selection is dropped into a Group, add the selected Parts into that Group;
            // if it fails, cancel the tool, rolling back any changes
            mouseDrop: finishDrop,
            handlesDragDropForMembers: true,  // don't need to define handlers on member Nodes and Links
            // Groups containing Groups lay out their members horizontally
            layout: $(go.LayeredDigraphLayout),
            background: "lightgray" // default background color
          },
          new go.Shape("Rectangle",
            { fill: null, stroke: "gray", strokeWidth: 2 }),
          $(go.Panel, "Vertical")  // title above Placeholder
            .add(
              $(go.Panel, "Horizontal",  // button next to TextBlock
                { stretch: go.Stretch.Horizontal })
                .add(
                  $("SubGraphExpanderButton", { alignment: go.Spot.Right, margin: 5 }),
                  $(go.TextBlock,
                    {
                      alignment: go.Spot.Left,
                      editable: true,
                      margin: new go.Margin(6, 10, 6, 1),
                      font: "bold 16px Lora, serif",
                      opacity: 0.95,  // allow some color to show through
                      stroke: "black"
                    })
                    .bind("text", "text")
                ),  // end Horizontal Panel
              $(go.Placeholder,
                { padding: 8, alignment: go.Spot.TopLeft })
            ),  // end Vertical Panel
          // Add ports to the group
          makePort("T", go.Spot.Top, false, true),
          makePort("L", go.Spot.Left, true, true),
          makePort("R", go.Spot.Right, true, true),
          makePort("B", go.Spot.Bottom, true, false)
        );

      // Create the model data
      myDiagram.model = new go.GraphLinksModel(
        [
          { key: 1, isGroup: true, text: "Group 1" },
          { key: 2, group: 1, text: "Node 1" },
          { key: 3, group: 1, text: "Node 2" },
          { key: 4, text: "Node outside Group" }
        ],
        [
          { from: 2, to: 3 },
          { from: 4, to: 1, toPort: "T" },  // Link to the top port of the group
          { from: 1, to: 4, fromPort: "R" }  // Link from the right port of the group
        ]
      );
    }

    function highlightGroup(e, grp, show) {
      if (!grp) return;
      e.handled = true;
      if (show) {
        var tool = grp.diagram.toolManager.draggingTool;
        var map = tool.draggedParts || tool.copiedParts;
        if (grp.canAddMembers(map.toKeySet())) {
          grp.isHighlighted = true;
          return;
        }
      }
      grp.isHighlighted = false;
    }

    function finishDrop(e, grp) {
      var ok = grp.addMembers(grp.diagram.selection, true);
      if (!ok) grp.diagram.currentTool.doCancel();
    }

    init();
  </script>
</body>
</html>



new go.Group("Auto",
  {
    ungroupable: true,
    // highlight when dragging into the Group
    mouseDragEnter: (e, grp, prev) => highlightGroup(e, grp, true),
    mouseDragLeave: (e, grp, next) => highlightGroup(e, grp, false),
    computesBoundsAfterDrag: true,
    computesBoundsIncludingLocation: true,
    // when the selection is dropped into a Group, add the selected Parts into that Group;
    // if it fails, cancel the tool, rolling back any changes
    mouseDrop: finishDrop,
    handlesDragDropForMembers: true,  // don't need to define handlers on member Nodes and Links
    // Groups containing Groups lay out their members horizontally
    layout: makeLayout(false),
    background: defaultColor(false) // default value if not specified in data
  })
  .bind("background", "horiz", defaultColor)
  .bind("layout", "horiz", makeLayout)
  .add(
    new go.Shape("Rectangle",
      { stroke: colors.gray, strokeWidth: 1, hasShadow: true })
      .bindObject("fill", "isHighlighted", h => h ? 'rgba(0,255,0,.3)' : 'transparent'),
    new go.Panel("Vertical")  // title above Placeholder
      .add(
        new go.Panel("Horizontal",  // button next to TextBlock
          { stretch: go.Stretch.Horizontal })
          .add(
            go.GraphObject.make("SubGraphExpanderButton", { alignment: go.Spot.Right, margin: 5 }),
            new go.TextBlock(
              {
                alignment: go.Spot.Left,
                editable: true,
                margin: new go.Margin(6, 10, 6, 1),
                font: "bold 16px Lora, serif",
                opacity: 0.95,  // allow some color to show through
                stroke: colors.black
              })
              .bind("font", "horiz", (horiz) => horiz ? "bold 20px Lora, serif" : "bold 16px Lora, serif")
              .bindTwoWay("text")
          ),  // end Horizontal Panel
        new go.Placeholder({ padding: 8, margin: 4, alignment: go.Spot.TopLeft }),
        // Add link section
        new go.Panel("Horizontal", { margin: new go.Margin(5, 0, 0, 0) })
          .add(
            new go.TextBlock("Link: "),
            new go.TextBlock(
              {
                textAlign: "center",
                cursor: "pointer",
                isUnderline: true,
                stroke: "blue",
                click: (e, obj) => {
                  var link = obj.part.data.link;
                  if (link) window.open(link, "_blank");
                }
              })
              .bind("text", "linkText")
          )  // end Horizontal Panel for link
      )  // end Vertical Panel
  )  // end Auto Panel





import * as go from 'gojs';

// Define interfaces for node and group data
interface NodeData {
  key: string;
  category: string;
  loc: string;
  size: string;
  text: string;
}

interface GroupData {
  key: string;
  category: string;
  loc: string;
  text: string;
}

export const initDiagram = (): go.Diagram => {
  const $ = go.GraphObject.make;

  const diagram = $(go.Diagram, {
    'undoManager.isEnabled': true  // enable undo & redo
  });

  // Node templates
  diagram.nodeTemplateMap.add("Ellipse",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Ellipse",
        { fill: "lightblue" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  diagram.nodeTemplateMap.add("Diamond",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Diamond",
        { fill: "lightgreen" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  diagram.nodeTemplateMap.add("RoundedRectangle",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "lightcoral" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Group template for RoundedRectangle
  diagram.groupTemplateMap.add("RoundedRectangleGroup",
    $(go.Group, "Auto",
      {
        layout: $(go.LayeredDigraphLayout),
        computesBoundsAfterDrag: true,  // allows members to resize when inside the group
        handlesDragDropForMembers: true // enables drag-and-drop from the group onto the diagram
      },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "rgba(128,128,128,0.2)", stroke: "gray", strokeWidth: 2 }),
      $(go.Panel, "Vertical",
        { defaultAlignment: go.Spot.TopLeft },
        $(go.TextBlock,
          { font: "Bold 12pt Sans-Serif", margin: 4 },
          new go.Binding("text")),
        $(go.Placeholder, { padding: 5 })
      )
    ));

  // Link template
  diagram.linkTemplate = $(
    go.Link,
    { routing: go.Link.AvoidsNodes, corner: 5 },
    $(go.Shape),  // the link shape
    $(go.Shape, { toArrow: 'Standard' })  // the arrowhead
  );

  // Override Diagram.validCycle to enforce containment rules
  diagram.validCycle = function(this: go.Diagram, fromNode: go.Node, toNode: go.Node | null, directed: boolean | null): boolean {
    if (fromNode.containingGroup?.category === "RoundedRectangleGroup") {
      if (toNode && toNode.category !== "RoundedRectangle") {
        return false;
      }
    }
    return go.Diagram.prototype.validCycle.call(this, fromNode, toNode, directed as boolean);
  };

  return diagram;
};



import * as go from 'gojs';

// Define interfaces for node and group data
interface NodeData {
  key: string;
  category: string;
  loc: string;
  size: string;
  text: string;
}

interface GroupData {
  key: string;
  category: string;
  loc: string;
  text: string;
}

export const initDiagram = (): go.Diagram => {
  const $ = go.GraphObject.make;

  const diagram = $(go.Diagram, {
    'undoManager.isEnabled': true  // enable undo & redo
  });

  // Node templates
  diagram.nodeTemplateMap.add("Ellipse",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Ellipse",
        { fill: "lightblue" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  diagram.nodeTemplateMap.add("Diamond",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Diamond",
        { fill: "lightgreen" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  diagram.nodeTemplateMap.add("RoundedRectangle",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "lightcoral" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Group template for RoundedRectangle
  diagram.groupTemplateMap.add("RoundedRectangleGroup",
    $(go.Group, "Auto",
      {
        layout: $(go.LayeredDigraphLayout),
        computesBoundsAfterDrag: true,  // allows members to resize when inside the group
        handlesDragDropForMembers: true // enables drag-and-drop from the group onto the diagram
      },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "rgba(128,128,128,0.2)", stroke: "gray", strokeWidth: 2 }),
      $(go.Panel, "Vertical",
        { defaultAlignment: go.Spot.TopLeft },
        $(go.TextBlock,
          { font: "Bold 12pt Sans-Serif", margin: 4 },
          new go.Binding("text")),
        $(go.Placeholder, { padding: 5 })
      )
    ));

  // Link template
  diagram.linkTemplate = $(
    go.Link,
    { routing: go.Link.AvoidsNodes, corner: 5 },
    $(go.Shape),  // the link shape
    $(go.Shape, { toArrow: 'Standard' })  // the arrowhead
  );

  // Override Diagram.validCycle to enforce containment rules
  diagram.validCycle = function(fromNode: go.Node, toNode: go.Node | null, directed: boolean | null): boolean {
    if (fromNode.containingGroup?.category === "RoundedRectangleGroup") {
      if (toNode && toNode.category !== "RoundedRectangle") {
        return false;
      }
    }
    return go.Diagram.prototype.validCycle.call(diagram, fromNode, toNode, directed as boolean);
  };

  return diagram;
};



import * as go from 'gojs';

// Define interfaces for node and group data
interface NodeData {
  key: string;
  category: string;
  loc: string;
  size: string;
  text: string;
}

interface GroupData {
  key: string;
  category: string;
  loc: string;
  text: string;
}

export const initDiagram = (): go.Diagram => {
  const $ = go.GraphObject.make;

  const diagram = $(go.Diagram, {
    'undoManager.isEnabled': true  // enable undo & redo
  });

  // Node templates
  diagram.nodeTemplateMap.add("Ellipse",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Ellipse",
        { fill: "lightblue" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  diagram.nodeTemplateMap.add("Diamond",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Diamond",
        { fill: "lightgreen" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  diagram.nodeTemplateMap.add("RoundedRectangle",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "lightcoral" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Group template for RoundedRectangle
  diagram.groupTemplateMap.add("RoundedRectangleGroup",
    $(go.Group, "Auto",
      {
        layout: $(go.LayeredDigraphLayout),
        computesBoundsAfterDrag: true,  // allows members to resize when inside the group
        handlesDragDropForMembers: true // enables drag-and-drop from the group onto the diagram
      },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "rgba(128,128,128,0.2)", stroke: "gray", strokeWidth: 2 }),
      $(go.Panel, "Vertical",
        { defaultAlignment: go.Spot.TopLeft },
        $(go.TextBlock,
          { font: "Bold 12pt Sans-Serif", margin: 4 },
          new go.Binding("text")),
        $(go.Placeholder, { padding: 5 })
      )
    ));

  // Link template
  diagram.linkTemplate = $(
    go.Link,
    { routing: go.Link.AvoidsNodes, corner: 5 },
    $(go.Shape),  // the link shape
    $(go.Shape, { toArrow: 'Standard' })  // the arrowhead
  );

  // Override Diagram.validCycle to enforce containment rules
  diagram.validCycle = (fromNode: go.Node, toNode: go.Node, directed: boolean) => {
    if (fromNode.containingGroup?.category === "RoundedRectangleGroup" && toNode?.category !== "RoundedRectangle") {
      return null;
    }
    return go.Diagram.prototype.validCycle.call(diagram, fromNode, toNode, directed);
  };

  return diagram;
};



import * as go from 'gojs';

export const initDiagram = (): go.Diagram => {
  const $ = go.GraphObject.make;

  const diagram = $(go.Diagram, {
    'undoManager.isEnabled': true  // enable undo & redo
  });

  // Node templates
  diagram.nodeTemplateMap.add("Ellipse",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Ellipse",
        { fill: "lightblue" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  diagram.nodeTemplateMap.add("Diamond",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Diamond",
        { fill: "lightgreen" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  diagram.nodeTemplateMap.add("RoundedRectangle",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "lightcoral" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Group template for RoundedRectangle
  diagram.groupTemplateMap.add("RoundedRectangleGroup",
    $(go.Group, "Auto",
      {
        layout: $(go.LayeredDigraphLayout),
        computesBoundsAfterDrag: true,  // allows members to resize when inside the group
        handlesDragDropForMembers: true // enables drag-and-drop from the group onto the diagram
      },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "rgba(128,128,128,0.2)", stroke: "gray", strokeWidth: 2 }),
      $(go.Panel, "Vertical",
        { defaultAlignment: go.Spot.TopLeft },
        $(go.TextBlock,
          { font: "Bold 12pt Sans-Serif", margin: 4 },
          new go.Binding("text")),
        $(go.Placeholder, { padding: 5 })
      )
    ));

  // Link template
  diagram.linkTemplate = $(
    go.Link,
    { routing: go.Link.AvoidsNodes, corner: 5 },
    $(go.Shape),  // the link shape
    $(go.Shape, { toArrow: 'Standard' })  // the arrowhead
  );

  // Override Diagram.validCycle to enforce containment rules
  diagram.validCycle = (fromNode, toNode, directed) => {
    if (fromNode.containingGroup?.category === "RoundedRectangleGroup" && toNode?.category !== "RoundedRectangle") {
      return null;
    }
    return super.validCycle(fromNode, toNode, directed);
  };

  return diagram;
};




import * as go from 'gojs';

export const initDiagram = (): go.Diagram => {
  const $ = go.GraphObject.make;

  const diagram = $(go.Diagram, {
    'undoManager.isEnabled': true  // enable undo & redo
  });

  // Node templates
  diagram.nodeTemplateMap.add("Ellipse",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Ellipse",
        { fill: "lightblue" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  diagram.nodeTemplateMap.add("Diamond",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Diamond",
        { fill: "lightgreen" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  diagram.nodeTemplateMap.add("RoundedRectangle",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "lightcoral" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Group template for RoundedRectangle
  diagram.groupTemplateMap.add("RoundedRectangleGroup",
    $(go.Group, "Auto",
      {
        layout: $(go.LayeredDigraphLayout),
        computesBoundsAfterDrag: true,  // allows members to resize when inside the group
        handlesDragDropForMembers: true // enables drag-and-drop from the group onto the diagram
      },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "rgba(128,128,128,0.2)", stroke: "gray", strokeWidth: 2 }),
      $(go.Panel, "Vertical",
        { defaultAlignment: go.Spot.TopLeft },
        $(go.TextBlock,
          { font: "Bold 12pt Sans-Serif", margin: 4 },
          new go.Binding("text")),
        $(go.Placeholder, { padding: 5 })
      )
    ));

  // Link template
  diagram.linkTemplate = $(
    go.Link,
    { routing: go.Link.AvoidsNodes, corner: 5 },
    $(go.Shape),  // the link shape
    $(go.Shape, { toArrow: 'Standard' })  // the arrowhead
  );

  // Override DraggingTool.computeEffectiveCollection to enforce containment rules
  diagram.toolManager.draggingTool.computeEffectiveCollection = (parts: go.Iterator<go.Part>, options?: go.DraggingTool.ComputeEffectiveCollectionOptions): go.Set<go.Part> => {
    const result = go.DraggingTool.prototype.computeEffectiveCollection.call(diagram.toolManager.draggingTool, parts, options);
    const group = options?.targetPart?.adornedPart as go.Group;

    if (group && group.category === "RoundedRectangleGroup") {
      const canContainOtherFigures = parts.every(part => {
        const partCategory = part.category;
        return partCategory === "Ellipse" || partCategory === "Diamond";
      });

      if (!canContainOtherFigures) {
        return new go.Set(); // Return an empty Set to prevent invalid drops
      }
    }

    return result;
  };

  return diagram;
};



import * as go from 'gojs';

export const initDiagram = (): go.Diagram => {
  const $ = go.GraphObject.make;

  const diagram = $(go.Diagram, {
    'undoManager.isEnabled': true  // enable undo & redo
  });

  // Node template for Ellipse
  diagram.nodeTemplateMap.add("Ellipse",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Ellipse",
        { fill: "lightblue" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Node template for Diamond
  diagram.nodeTemplateMap.add("Diamond",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Diamond",
        { fill: "lightgreen" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Node template for RoundedRectangle
  diagram.nodeTemplateMap.add("RoundedRectangle",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "lightcoral" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Group template for RoundedRectangle
  diagram.groupTemplateMap.add("RoundedRectangle",
    $(go.Group, "Auto",
      {
        layout: $(go.LayeredDigraphLayout),
        // Allow the group to contain other nodes
        computesBoundsAfterDrag: true,
        handlesDragDropForMembers: true
      },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "rgba(128,128,128,0.2)", stroke: "gray", strokeWidth: 2 }),
      $(go.Panel, "Vertical",
        { defaultAlignment: go.Spot.TopLeft },
        $(go.TextBlock,
          { font: "Bold 12pt Sans-Serif", margin: 4 },
          new go.Binding("text")),
        $(go.Placeholder, { padding: 5 })
      )
    ));

  // Link template
  diagram.linkTemplate = $(
    go.Link,
    { routing: go.Link.AvoidsNodes, corner: 5 },
    $(go.Shape),  // the link shape
    $(go.Shape, { toArrow: 'Standard' })  // the arrowhead
  );

  // Override DraggingTool.computeEffectiveCollection to enforce containment rules
  diagram.toolManager.draggingTool.computeEffectiveCollection = (parts: go.Iterator<go.Part>, options?: go.DraggingTool.ComputeEffectiveCollectionOptions): go.Set<go.Part> => {
    const result = go.DraggingTool.prototype.computeEffectiveCollection.call(this, parts, options);
    const group = options?.targetPart?.adornedPart as go.Group;
    
    if (group && group.category === "RoundedRectangle") {
      const canContainOtherFigures = parts.every(part => {
        const partCategory = part.category;
        return partCategory === "Ellipse" || partCategory === "Diamond";
      });

      if (!canContainOtherFigures) {
        return new go.Set(); // Return an empty Set to prevent invalid drops
      }
    }

    return result;
  };

  return diagram;
};



import * as go from 'gojs';

export function initDiagram(): go.Diagram {
  const $ = go.GraphObject.make;

  const diagram = $(go.Diagram, {
    'undoManager.isEnabled': true  // enable undo & redo
  });

  // Node template for Ellipse
  diagram.nodeTemplateMap.add("Ellipse",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Ellipse",
        { fill: "lightblue" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Node template for Diamond
  diagram.nodeTemplateMap.add("Diamond",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Diamond",
        { fill: "lightgreen" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Node template for RoundedRectangle
  diagram.nodeTemplateMap.add("RoundedRectangle",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "lightcoral" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Group template for RoundedRectangle
  diagram.groupTemplateMap.add("RoundedRectangle",
    $(go.Group, "Auto",
      {
        layout: $(go.LayeredDigraphLayout),
        // Allow the group to contain other nodes
        computesBoundsAfterDrag: true,
        handlesDragDropForMembers: true
      },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "rgba(128,128,128,0.2)", stroke: "gray", strokeWidth: 2 }),
      $(go.Panel, "Vertical",
        { defaultAlignment: go.Spot.TopLeft },
        $(go.TextBlock,
          { font: "Bold 12pt Sans-Serif", margin: 4 },
          new go.Binding("text")),
        $(go.Placeholder, { padding: 5 })
      )
    ));

  // Link template
  diagram.linkTemplate = $(
    go.Link,
    { routing: go.Link.AvoidsNodes, corner: 5 },
    $(go.Shape),  // the link shape
    $(go.Shape, { toArrow: 'Standard' })  // the arrowhead
  );

  // Override DraggingTool.computeEffectiveCollection to enforce containment rules
  diagram.toolManager.draggingTool.computeEffectiveCollection = function(parts, options) {
    const result = go.DraggingTool.prototype.computeEffectiveCollection.call(this, parts, options);
    const group = options?.targetPart?.adornedPart;
    
    if (group instanceof go.Group && group.category === "RoundedRectangle") {
      const canContainOtherFigures = parts.every(part => {
        const partCategory = part.category;
        return partCategory === "Ellipse" || partCategory === "Diamond";
      });

      if (!canContainOtherFigures) {
        return new go.Set(); // Return an empty Set to prevent invalid drops
      }
    }

    return result;
  };

  return diagram;
}




import * as go from 'gojs';

export function initDiagram(): go.Diagram {
  const $ = go.GraphObject.make;

  const diagram = $(go.Diagram, {
    'undoManager.isEnabled': true  // enable undo & redo
  });

  // Node template for Ellipse
  diagram.nodeTemplateMap.add("Ellipse",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Ellipse",
        { fill: "lightblue" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Node template for Diamond
  diagram.nodeTemplateMap.add("Diamond",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Diamond",
        { fill: "lightgreen" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Node template for RoundedRectangle
  diagram.nodeTemplateMap.add("RoundedRectangle",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "lightcoral" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Group template for RoundedRectangle
  diagram.groupTemplateMap.add("RoundedRectangle",
    $(go.Group, "Auto",
      {
        layout: $(go.LayeredDigraphLayout),
        // Allow the group to contain other nodes
        computesBoundsAfterDrag: true,
        handlesDragDropForMembers: true
      },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "rgba(128,128,128,0.2)", stroke: "gray", strokeWidth: 2 }),
      $(go.Panel, "Vertical",
        { defaultAlignment: go.Spot.TopLeft },
        $(go.TextBlock,
          { font: "Bold 12pt Sans-Serif", margin: 4 },
          new go.Binding("text")),
        $(go.Placeholder, { padding: 5 })
      )
    ));

  // Link template
  diagram.linkTemplate = $(
    go.Link,
    { routing: go.Link.AvoidsNodes, corner: 5 },
    $(go.Shape),  // the link shape
    $(go.Shape, { toArrow: 'Standard' })  // the arrowhead
  );

  // Adornment template for highlighting during drag-over
  diagram.nodeTemplateMap.add("Highlighted",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      $(go.Shape, "Rectangle",
        { fill: null, stroke: "dodgerblue", strokeWidth: 2 }),
      $(go.Placeholder)
    ));

  // Handle ExternalObjectsDropped event to validate group membership
  diagram.addDiagramListener("ExternalObjectsDropped", (e: go.DiagramEvent) => {
    const nodes = e.subject.toArray();
    const group = e.diagram.selection.first() as go.Group | null;

    if (group && group.category === "RoundedRectangle") {
      const canContainOtherFigures = nodes.every(node => {
        const nodeCategory = node.category;
        return nodeCategory === "Ellipse" || nodeCategory === "Diamond";
      });

      if (!canContainOtherFigures) {
        alert("RoundedRectangle can only contain Ellipse and Diamond nodes.");
        e.diagram.currentTool.doCancel();
      }
    }
  });

  // Override Group.computeMemberBounds to resize nodes to fit inside RoundedRectangle
  diagram.groupTemplateMap.add("RoundedRectangle",
    $(go.Group, "Auto",
      {
        layout: $(go.LayeredDigraphLayout),
        // Allow the group to contain other nodes
        computesBoundsAfterDrag: true,
        handlesDragDropForMembers: true,
        // Override computeMemberBounds to resize nodes to fit inside RoundedRectangle
        computeMemberBounds: function(member) {
          if (member instanceof go.Node && member.containingGroup && member.containingGroup.category === "RoundedRectangle") {
            const padding = 10; // adjust padding as needed
            const bb = member.actualBounds.copy();
            const gbb = member.containingGroup.actualBounds.copy();
            const newBounds = new go.Rect(gbb.x + padding, gbb.y + padding, gbb.width - 2 * padding, gbb.height - 2 * padding);
            if (newBounds.containsRect(bb)) return bb;
            const r = newBounds.copy().intersectRect(bb);
            member.move(new go.Point(r.x - bb.x, r.y - bb.y));
            return r;
          }
          return go.GraphObject.prototype.computeMemberBounds.call(this, member);
        }
      },
      $(go.Shape, "RoundedRectangle",
        { fill: "rgba(128,128,128,0.2)", stroke: "gray", strokeWidth: 2 }),
      $(go.Panel, "Vertical",
        { defaultAlignment: go.Spot.TopLeft },
        $(go.TextBlock,
          { font: "Bold 12pt Sans-Serif", margin: 4 },
          new go.Binding("text")),
        $(go.Placeholder, { padding: 5 })
      )
    ));

  return diagram;
}





import * as go from 'gojs';

export function initDiagram(): go.Diagram {
  const $ = go.GraphObject.make;

  const diagram = $(go.Diagram, {
    'undoManager.isEnabled': true  // enable undo & redo
  });

  // Node template for Ellipse
  diagram.nodeTemplateMap.add("Ellipse",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Ellipse",
        { fill: "lightblue" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Node template for Diamond
  diagram.nodeTemplateMap.add("Diamond",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Diamond",
        { fill: "lightgreen" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Node template for RoundedRectangle
  diagram.nodeTemplateMap.add("RoundedRectangle",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "lightcoral" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Group template for RoundedRectangle
  diagram.groupTemplateMap.add("RoundedRectangle",
    $(go.Group, "Auto",
      {
        layout: $(go.LayeredDigraphLayout),
        // Allow the group to contain other nodes
        computesBoundsAfterDrag: true,
        handlesDragDropForMembers: true
      },
      $(go.Shape, "RoundedRectangle",
        { fill: "rgba(128,128,128,0.2)", stroke: "gray", strokeWidth: 2 }),
      $(go.Panel, "Vertical",
        { defaultAlignment: go.Spot.TopLeft },
        $(go.TextBlock,
          { font: "Bold 12pt Sans-Serif", margin: 4 },
          new go.Binding("text")),
        $(go.Placeholder, { padding: 5 })
      )
    ));

  // Link template
  diagram.linkTemplate = $(
    go.Link,
    { routing: go.Link.AvoidsNodes, corner: 5 },
    $(go.Shape),  // the link shape
    $(go.Shape, { toArrow: 'Standard' })  // the arrowhead
  );

  // Handle ExternalObjectsDropped event to validate group membership
  diagram.addDiagramListener("ExternalObjectsDropped", (e: go.DiagramEvent) => {
    const nodes = e.subject.toArray();
    const group = e.diagram.selection.first() as go.Group | null;

    if (group && group.category === "RoundedRectangle") {
      const canContainOtherFigures = nodes.every(node => {
        const nodeCategory = node.category;
        return nodeCategory === "Ellipse" || nodeCategory === "Diamond";
      });

      if (!canContainOtherFigures) {
        alert("RoundedRectangle can only contain Ellipse and Diamond nodes.");
        e.diagram.currentTool.doCancel();
      }
    }
  });

  return diagram;
}




import * as go from 'gojs';

export function initDiagram(): go.Diagram {
  const $ = go.GraphObject.make;

  const diagram = $(go.Diagram, {
    'undoManager.isEnabled': true  // enable undo & redo
  });

  // Node template for Ellipse
  diagram.nodeTemplateMap.add("Ellipse",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Ellipse",
        { fill: "lightblue" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Node template for Diamond
  diagram.nodeTemplateMap.add("Diamond",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Diamond",
        { fill: "lightgreen" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Node template for RoundedRectangle
  diagram.nodeTemplateMap.add("RoundedRectangle",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "lightcoral" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Group template for RoundedRectangle
  diagram.groupTemplateMap.add("RoundedRectangle",
    $(go.Group, "Auto",
      {
        layout: $(go.LayeredDigraphLayout),
        // Allow the group to contain other nodes
        computesBoundsAfterDrag: true,
        handlesDragDropForMembers: true
      },
      $(go.Shape, "RoundedRectangle",
        { fill: "rgba(128,128,128,0.2)", stroke: "gray", strokeWidth: 2 }),
      $(go.Panel, "Vertical",
        { defaultAlignment: go.Spot.TopLeft },
        $(go.TextBlock,
          { font: "Bold 12pt Sans-Serif", margin: 4 },
          new go.Binding("text")),
        $(go.Placeholder, { padding: 5 })
      )
    ));

  // Link template
  diagram.linkTemplate = $(
    go.Link,
    { routing: go.Link.AvoidsNodes, corner: 5 },
    $(go.Shape),  // the link shape
    $(go.Shape, { toArrow: 'Standard' })  // the arrowhead
  );

  // Handle ExternalObjectsDropped event to validate group membership
  diagram.addDiagramListener("ExternalObjectsDropped", (e: go.DiagramEvent) => {
    const nodes = e.subject.cast<go.PartManager>().toKeySet().toArray();
    const group = e.diagram.selection.first() as go.Group | null;

    if (group && group.category === "RoundedRectangle") {
      const canContainOtherFigures = nodes.every(node => {
        const nodeCategory = node.category;
        return nodeCategory === "Ellipse" || nodeCategory === "Diamond";
      });

      if (!canContainOtherFigures) {
        alert("RoundedRectangle can only contain Ellipse and Diamond nodes.");
        e.diagram.currentTool.doCancel();
      }
    }
  });

  return diagram;
}




import * as go from 'gojs';

export function initDiagram(): go.Diagram {
  const $ = go.GraphObject.make;

  const diagram = $(go.Diagram, {
    'undoManager.isEnabled': true  // enable undo & redo
  });

  // Node template for Ellipse
  diagram.nodeTemplateMap.add("Ellipse",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Ellipse",
        { fill: "lightblue" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Node template for Diamond
  diagram.nodeTemplateMap.add("Diamond",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Diamond",
        { fill: "lightgreen" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Node template for RoundedRectangle
  diagram.nodeTemplateMap.add("RoundedRectangle",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "lightcoral" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Group template for RoundedRectangle
  diagram.groupTemplateMap.add("RoundedRectangle",
    $(go.Group, "Auto",
      {
        layout: $(go.LayeredDigraphLayout),
        // Allow the group to contain other nodes
        computesBoundsAfterDrag: true,
        handlesDragDropForMembers: true
      },
      $(go.Shape, "RoundedRectangle",
        { fill: "rgba(128,128,128,0.2)", stroke: "gray", strokeWidth: 2 }),
      $(go.Panel, "Vertical",
        { defaultAlignment: go.Spot.TopLeft },
        $(go.TextBlock,
          { font: "Bold 12pt Sans-Serif", margin: 4 },
          new go.Binding("text")),
        $(go.Placeholder, { padding: 5 })
      )
    ));

  // Link template
  diagram.linkTemplate = $(
    go.Link,
    { routing: go.Link.AvoidsNodes, corner: 5 },
    $(go.Shape),  // the link shape
    $(go.Shape, { toArrow: 'Standard' })  // the arrowhead
  );

  // Handle ExternalObjectsDropped event to validate group membership
  diagram.addDiagramListener("ExternalObjectsDropped", function (e: go.DiagramEvent) {
    const nodes = e.subject.cast<go.PartManager>().toKeySet().toArray();
    const group = e.diagram.selection.first() as go.Group | null;

    if (group && group.category === "RoundedRectangle") {
      const canContainOtherFigures = nodes.every(node => {
        const nodeCategory = node.category;
        return nodeCategory === "Ellipse" || nodeCategory === "Diamond";
      });

      if (!canContainOtherFigures) {
        alert("RoundedRectangle can only contain Ellipse and Diamond nodes.");
        e.diagram.currentTool.doCancel();
      }
    }
  });

  return diagram;
}






import * as go from 'gojs';

export function initDiagram(): go.Diagram {
  const $ = go.GraphObject.make;

  const diagram = $(go.Diagram, {
    'undoManager.isEnabled': true  // enable undo & redo
  });

  // Node template for Ellipse
  diagram.nodeTemplateMap.add("Ellipse",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Ellipse",
        { fill: "lightblue" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Node template for Diamond
  diagram.nodeTemplateMap.add("Diamond",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "Diamond",
        { fill: "lightgreen" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Node template for RoundedRectangle
  diagram.nodeTemplateMap.add("RoundedRectangle",
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape, "RoundedRectangle",
        { fill: "lightcoral" }),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  // Group template for RoundedRectangle
  diagram.groupTemplateMap.add("RoundedRectangle",
    $(go.Group, "Auto",
      {
        layout: $(go.LayeredDigraphLayout),
        // Allow the group to contain other nodes
        computesBoundsAfterDrag: true,
        handlesDragDropForMembers: true
      },
      $(go.Shape, "RoundedRectangle",
        { fill: "rgba(128,128,128,0.2)", stroke: "gray", strokeWidth: 2 }),
      $(go.Panel, "Vertical",
        { defaultAlignment: go.Spot.TopLeft },
        $(go.TextBlock,
          { font: "Bold 12pt Sans-Serif", margin: 4 },
          new go.Binding("text")),
        $(go.Placeholder, { padding: 5 })
      )
    ));

  // Link template
  diagram.linkTemplate = $(
    go.Link,
    { routing: go.Link.AvoidsNodes, corner: 5 },
    $(go.Shape),  // the link shape
    $(go.Shape, { toArrow: 'Standard' })  // the arrowhead
  );

  // Handle ExternalObjectsDropped event to validate group membership
  diagram.addDiagramListener("ExternalObjectsDropped", function (e: go.DiagramEvent) {
    const nodes = e.subject.cast<go.PartManager>().toKeySet().toArray();
    const group = e.diagram.selection.first() as go.Group | null;

    if (group && group.category === "RoundedRectangle") {
      const canContainOtherFigures = nodes.every(node => {
        const nodeCategory = node.category;
        return nodeCategory === "Ellipse" || nodeCategory === "Diamond";
      });

      if (!canContainOtherFigures) {
       ​⬤




import * as go from 'gojs';

export function initDiagram(): go.Diagram {
  const $ = go.GraphObject.make;

  const diagram = $(go.Diagram, {
    'undoManager.isEnabled': true  // enable undo & redo
  });

  diagram.toolManager.linkingTool.linkValidation = validateLink;
  diagram.toolManager.relinkingTool.linkValidation = validateLink;

  diagram.nodeTemplateMap.add("",  // default category
    $(go.Node, "Auto",
      { locationSpot: go.Spot.Center },
      new go.Binding("location", "loc", go.Point.parse).makeTwoWay(go.Point.stringify),
      new go.Binding("desiredSize", "size", go.Size.parse).makeTwoWay(go.Size.stringify),
      $(go.Shape,
        new go.Binding("figure", "figure"),
        new go.Binding("fill", "color")),
      $(go.TextBlock, { margin: 8 },
        new go.Binding("text", "text"))
    ));

  diagram.groupTemplateMap.add("RoundedRectangle",
    $(go.Group, "Auto",
      {
        layout: $(go.LayeredDigraphLayout),
        // ensure the group is able to contain other nodes
        computesBoundsAfterDrag: true,
        handlesDragDropForMembers: true,
        memberValidation: validateContainment
      },
      $(go.Shape, "RoundedRectangle",
        { fill: "rgba(128,128,128,0.2)", stroke: "gray", strokeWidth: 2 }),
      $(go.Panel, "Vertical",
        { defaultAlignment: go.Spot.TopLeft },
        $(go.TextBlock,
          { font: "Bold 12pt Sans-Serif", margin: 4 },
          new go.Binding("text")),
        $(go.Placeholder, { padding: 5 })
      )
    ));

  diagram.linkTemplate = $(
    go.Link,
    { routing: go.Link.AvoidsNodes, corner: 5 },
    $(go.Shape),  // the link shape
    $(go.Shape, { toArrow: 'Standard' })  // the arrowhead
  );

  return diagram;
}

function validateLink(fromNode: go.Node, toNode: go.Node): boolean {
  const fromFigure = fromNode.data.figure;
  const toFigure = toNode.data.figure;

  if (fromFigure === 'Ellipse' && (toFigure === 'Ellipse' || toFigure === 'RoundedRectangle')) {
    return true;
  } else if (fromFigure === 'RoundedRectangle' && toFigure === 'RoundedRectangle') {
    return true;
  }
  return false;
}

function validateContainment(group: go.Group, node: go.Node): boolean {
  return true;  // Allow all nodes to be contained in the RoundedRectangle group
}




import { updatedLinkStyle } from './gojs-inits';
import * as go from 'gojs';

describe('updatedLinkStyle', () => {
  let diagram: go.Diagram;

  beforeEach(() => {
    // Create a mock diagram for testing
    diagram = new go.Diagram();
    // Create and add nodes and links to the diagram as needed for tests
    const dashedDiamond = { category: 'DashedDiamond' };
    const dashedCircle = { category: 'DashedCircle' };
    const node1 = { key: 1, data: dashedDiamond };
    const node2 = { key: 2, data: dashedCircle };
    diagram.model = new go.GraphLinksModel([node1, node2], []);
    const link = { from: 1, to: 2 };
    diagram.model.addLinkData(link);
    diagram.commit((d) => {
      updatedLinkStyle(d);
    }, 'updated style');
  });

  it('should update link style for compatible nodes', () => {
    const link = diagram.links.first();
    const path = link.path as go.Shape;
    expect(path.strokeDashArray).toEqual([4, 4]);
    expect(link.elt(1).visible).toBeFalse();
  });

  it('should reset link style for other nodes', () => {
    const link = diagram.links.first();
    const path = link.path as go.Shape;
    // Assuming link is not between DashedDiamond and DashedCircle nodes
    expect(path.strokeDashArray).toBeNull();
    expect(link.elt(1).visible).toBeTrue();
  });
});



export function updatedLinkStyle(diagram: go.Diagram) {
  diagram.links.each(function (link) {
    const fromNode = link.fromNode;
    const toNode = link.toNode;
    if (
      (fromNode &&
        toNode &&
        fromNode.data?.category === 'DashedDiamond' &&
        toNode.data?.category === 'DashedCircle') ||
      (fromNode &&
        toNode &&
        fromNode.data?.category === 'DashedCircle' &&
        toNode.data?.category === 'DashedDiamond')
    ) {
      link.path!.strokeDashArray = [4, 4];
      link.elt(1).visible = false;
    } else {
      link.path!.strokeDashArray = null;
      link.elt(1).visible = true;
    }
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
    const mouseEnterEvent = new go.InputEvent();
    mouseEnterEvent.targetDiagram = component.diagram;
    component.diagram.toolManager.doMouseEnter(mouseEnterEvent, node);

    node.ports.each((port) => {
      if (port.portId !== '') {
        expect(port.fill).toBe('rgba(0,0,0,.3)');
      }
    });

    // Simulate mouse leave
    const mouseLeaveEvent = new go.InputEvent();
    mouseLeaveEvent.targetDiagram = component.diagram;
    component.diagram.toolManager.doMouseLeave(mouseLeaveEvent, node);

    node.ports.each((port) => {
      if (port.portId !== '') {
        expect(port.fill).toBe(null);
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
