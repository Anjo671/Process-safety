# Creating a new project

1. Open TwinCAT XAE shell
2. Create a new project
   ![new project](./create_new_project.png)
3. Provide a location and name
   ![location and name](./choose_location_and_name.png)
4. You have now created an empty project. Your solution explorer should look like so:
   ![empty project](./empty_project.png)
5. Since you have now an empty project, you must tell TC3 that you have a PLC as part of your project. Right mouse click on `PLC` in te tree, and `add a new item`
   ![add new item](./add_new_item.png)
6. Add a new `standard PLC project` to the project, in this case it will be the Virtual PLC that we're going to program.
   ![add vplc](./add_vplc.png)
7. Expand the PLC item in the solution explorer. You should now be able to add files to your PLC project in te same manner as you would with CODESYS. Your trees should look like so:
   ![added plc project](./plc_project_added.png)