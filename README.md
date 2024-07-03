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
