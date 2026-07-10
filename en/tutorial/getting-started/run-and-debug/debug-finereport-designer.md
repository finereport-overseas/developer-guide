## Locating Component Source Code with Designer4Debug

During development, you often need to find the source class corresponding to a UI component in the designer. You can do this visually using **Designer4Debug + SwingExplorer**.

### Steps

1. Make sure you are using the latest [designer source code](https://github.com/finereport-overseas/report-starter-11).

2. When launching the designer, select **Designer4Debug** — i.e., use the main class `com.fr.start.Designer4Debug`:

   ![Select the Designer4Debug entry point](images/designer_debug_entrance.png)

3. After startup, both the designer and a **Swing Explorer** window will open:

   ![Designer4Debug startup result](images/designer_debug_start.png)

4. Bring the Swing Explorer window to the front and select a tree node in it:

   ![Swing Explorer tree node](images/designer_debug_tree.png)

5. Click the **"Display selected component"** button:

   ![Click Display selected component](images/designer_debug_select.png)

6. The component display area in Swing Explorer will now mirror the designer's interface in real time.

7. Click a component in the designer — the code tree on the left will highlight the corresponding class:

   ![Locating the template tree widget](images/designer_debug_template_tree.png)

   ![Locating the dataset tab widget](images/designer_debug_dataset_tab.png)

8. Search for that class in your IDE to find the source code:

   ![Search result in IDE](images/designer_debug_result.png)

9. **Locating components inside pop-up dialogs**: Switch to the designer window and open the dialog via the relevant menu, then switch back to Swing Explorer and click Refresh. The new dialog will appear and you can locate its components using the same steps above.
