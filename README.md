# IK-FK Control Snapper

Keep your armatures clean and your animations fluid. **IK-FK Control Snapper** lets you match, switch, and snap FK and IK controls for arms and legs in Blender.

Built for Blender 4.2+.

---

## What it does

- Snap FK controls to an IK pose
- Snap IK controls to an FK pose
- Switch FK/IK influence from a dedicated control bone
- Configure the left arm, right arm, left leg, and right leg separately
- Find every tool in the 3D View sidebar under **Rig Tools**

---

## Install

1. Download this repository as a ZIP.
2. Open Blender and go to **Edit > Preferences**.
3. Open **Extensions** or **Add-ons**.
4. Choose **Install from Disk**.
5. Select the ZIP and enable **IK-FK Snapper**.


---

## Quick start

1. Select the armature.
2. Enter **Pose Mode**.
3. Open the 3D View sidebar with `N`.
4. Open **Rig Tools**.
5. Expand a limb and assign its bones in **Bone Settings**.

### Bone setup

| Limb field | Assign |
| --- | --- |
| FK Upper / Lower / End | Upper, lower, and end FK control bones |
| Deform Upper / Lower / End | Matching deform or driven bones |
| IK Target | Hand or foot IK control |
| IK Pole | Elbow or knee pole control |

The names do not need to match, and each of the four limbs has its own settings.

---

## Snap a pose

Each limb has two snapping buttons:

| Button | What happens |
| --- | --- |
| **FK -> IK** | Moves the FK controls to match the IK/deform pose |
| **IK -> FK** | Moves the IK target and pole to match the FK pose |

These operations run on the currently selected armature and are available in Pose Mode.

**Smooth switching tip:** snap the controls first, then switch influence. The poses will match before the rig changes from FK to IK or back.

---

## Create a switcher

The switcher is a control bone that holds two numeric custom properties, such as:

```text
FK_S = 1
IK_S = 0
```

These properties drive the influence of Copy Transforms constraints. The add-on does not copy transforms or change constraints by itself—it only flips the two properties.

### 1. Make the control bone

Create or choose a dedicated switcher bone.

Lock or hide its transforms if you do not want it to move. Add two numeric custom properties:

- `FK_S`
- `IK_S`

Keep one value at `1` and the other at `0`.

### 2. Add Copy Transforms constraints

On each deform or driven bone, add two Copy Transforms constraints:

| Constraint target | Purpose |
| --- | --- |
| FK control bone | Copy the FK control when FK is active |
| IK control bone | Copy the IK control when IK is active |

Repeat this for the upper, lower, and end deform bones that need the switch.

### 3. Drive the constraint influences

Use a property as the **driver variable** for each constraint's **Influence** value:

- Driver `FK_S` -> Copy Transforms constraint targeting the FK control
- Driver `IK_S` -> Copy Transforms constraint targeting the IK control

To use a switcher property as a driver:

1. Open the constraint settings.
2. Right-click the **Influence** value and choose **Add Driver**.
3. Open the Drivers editor.
4. Choose the switcher armature or bone property as the variable.
5. Connect it to the constraint influence.
6. Repeat for every Copy Transforms constraint.

### 4. Connect the switcher

In **Rig Tools**, choose the switcher bone under **Switcher**. The add-on recognizes the first two numeric custom properties and displays their current values.

Press **Toggle FK / IK** to flip:

```text
FK_S = 0  <->  1
IK_S = 1  <->  0
```

The drivers update the constraint influences, changing from FK to IK or from IK to FK.

---

## Troubleshooting

| Problem | Solution |
| --- | --- |
| `Enter Pose Mode to use` | Select the armature and enter Pose Mode |
| `FK/IK settings not initialized` | Disable and re-enable the add-on, then reselect the armature |
| `Switcher bone needs 2 numeric custom properties` | Add at least two numeric properties such as `FK_S` and `IK_S` |
| A bone is not found | Check the limb settings and confirm that the name belongs to a pose bone |
| Switching does not change the pose | Check the Copy Transforms targets and driver variables for Influence |

The switcher ignores `_RNA_UI` and Boolean properties. It only reads the first two numeric custom properties found on the selected bone.

---

## Development

Source: [`ikfk_snapping/__init__.py`](ikfk_snapping/__init__.py)

Metadata: [`ikfk_snapping/blender_manifest.toml`](ikfk_snapping/blender_manifest.toml)

Test changes in Blender by disabling and re-enabling the add-on, then checking a switch and snap operation for each limb.

---

## License

GNU General Public License v3.0 or later.
