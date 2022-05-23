# Pattern
Even though no pattern is enforced. It is always recommended to use one.

## MVC
To adapt to the MVC-pattern you have to create the controller and initialize the model and the view inside the controller.
The controller does only have the view as child, which is taking care of displaying the UI.
The model is communicating with the repositories / managers via scene and returning the data back to the controller.
The connections between controller, view and model can be set up with fields and observers.