# Azure Immersive Reader - Immersive Reader 

1. An Immersive Reader resource configured for Azure Active Directory authentication. Follow to get set up [here](./setup.pdf). You will need some of the values created here when configuring the environment properties. Save the output of your session into a text file for future reference.

2. Change the directory into js/samples/quickstart-python.

3. Replace the values in global_variables.py with your values

4. Run the below code. Please replace the values with your values

   ```bash
   az webapp up -n immersivereaderapp --runtime "python:3.8"
   ```

5. After few minutes,  your immersive app is running. You can embed them in the webapp.

