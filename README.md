# ConnectFour-Game
Main.java
package com.connect4games;

import javafx.application.Application;
import javafx.application.Platform;
import javafx.fxml.FXMLLoader;
import javafx.scene.Scene;
import javafx.scene.control.*;
import javafx.scene.layout.GridPane;
import javafx.scene.layout.Pane;
import javafx.stage.Stage;

public class Main extends Application {

    public static void main(String[] args) {
        launch(args);
    }

    private  Controller controller; //controller as field variable
    public void start(Stage primaryStage) throws Exception {
        FXMLLoader loader = new FXMLLoader(getClass().getResource("Connect4games.fxml"));//loading fxml file
        GridPane rootnode=loader.load(); //loading the rootnode
        controller=loader.getController();  //loading controller
        controller.createplayground();

        MenuBar menuBar=createMenu();
        Pane menuPane = (Pane) rootnode.getChildren().get(0);
        menuPane.getChildren().add(menuBar);
        menuBar.prefWidthProperty().bind(primaryStage.widthProperty()); //menubar equal to size of primary stage
        Scene scene = new Scene(rootnode);
        primaryStage.setScene(scene);
        primaryStage.setTitle("Connect 4");
        primaryStage.show();
    }
    private MenuBar createMenu(){
        //Creating file menu
        Menu fileMenu=new Menu("File");
        //Creating submenus under File menu
        MenuItem newGame=new MenuItem("New game");
        newGame.setOnAction(event->controller.resetGame());
        MenuItem resetGame=new MenuItem("Reset game");
        resetGame.setOnAction(actionEvent -> controller.resetGame());

        //Introducing separator between menuitems
        SeparatorMenuItem separatorMenuItem=new SeparatorMenuItem();
        MenuItem exitGame=new MenuItem("Exit game");
        exitGame.setOnAction(actionEvent -> { ExitGame();

        });
        //adding all submenus under File menu
        fileMenu.getItems().addAll(newGame,resetGame,exitGame);

         //creating help menu
        Menu helpMenu=new Menu("Help");
        //creating submenu named aboutGame and About Me
        MenuItem aboutGame=new MenuItem("About game");
        aboutGame.setOnAction(actionEvent -> AboutConnect4());
        SeparatorMenuItem separator=new SeparatorMenuItem();
        MenuItem aboutme=new MenuItem("About me");
        aboutme.setOnAction(actionEvent -> Aboutme());
        helpMenu.getItems().addAll(aboutGame,aboutme);
        MenuBar menuBar=new MenuBar();
        //adding submenu items under Help menu
        menuBar.getMenus().addAll(fileMenu,helpMenu);
        return menuBar;
    }
//about me method
    private void Aboutme() {
        Alert alert=new Alert(Alert.AlertType.INFORMATION);
        alert.setTitle("About Me");
        alert.setResizable(true);
        alert.setHeaderText("Sakshi");
        alert.setContentText("I am new to this.Currently learning more.This is my first game");
        alert.show();
    }
//ABout connect4 method
    private void AboutConnect4() {

        Alert alert=new Alert(Alert.AlertType.INFORMATION);
        alert.setTitle("About Connect Four");
        alert.setHeaderText("How to play?");
        alert.setResizable(true);
        alert.setContentText("Connect is a two-player connection board game, in which the players choose a color" +
                " and then take turns dropping colored discs into a seven-column, six-row vertically suspended grid." +
                " The pieces fall straight down, occupying the lowest available space within the column. " +
                "The objective of the game is to be the first to form a horizontal, vertical, or diagonal line of four of one's" +
                " own discs. Connect Four is a solved game. The first player can always win by playing the right moves.");
        alert.show();
    }
//Exit game
    private void ExitGame() {
        Platform.exit(); //closing platform
        System.exit(0);  //exiting system
    }

    private void resetGame() {

    }
}



=======================================================================================================================================================================
Controller.java

package com.connect4games;

import javafx.animation.TranslateTransition;
import javafx.application.Platform;
import javafx.fxml.FXML;
import javafx.fxml.FXMLLoader;
import javafx.fxml.Initializable;
import javafx.geometry.Point2D;
import javafx.scene.Scene;
import javafx.scene.control.Alert;
import javafx.scene.control.Button;
import javafx.scene.control.ButtonType;
import javafx.scene.control.Label;
import javafx.scene.control.TextField;
import javafx.scene.layout.GridPane;
import javafx.scene.layout.Pane;
import javafx.scene.paint.Color;
import javafx.scene.shape.Circle;
import javafx.scene.shape.Rectangle;
import javafx.scene.shape.Shape;
import javafx.stage.Stage;
import javafx.util.Duration;

import java.io.IOException;
import java.net.URL;
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;
import java.util.ResourceBundle;
import java.util.stream.Collectors;
import java.util.stream.IntStream;

public class Controller implements Initializable {

    private static  final int COLUMNS=7;
    private static  final int ROWS=6;
    private static  final int CIRCLE_DIAMETER=80;
    private static  final String discColor1="#24303E";
    private static  final String discColor2="#4CAA88";

    private  boolean isplayeroneturn =true;
    private boolean isallowedtoinsert=true;
    private Disc[][] inserteddiscarray=new Disc[ROWS][COLUMNS];
    @FXML
    GridPane rootGridPane;
    @FXML
    Pane insertedDiscsPane;
    @FXML
    Label playerNameLabel;
    @FXML
   public  TextField playeArOneTextField,playerTwoTextField;
    @FXML
    Button setNamesButton;
    public Button loadbtn;

    public  void createplayground(){
        //Rectangle is pre-defined java class in javafx
        Shape rectangleWithHoles=createGameStructuralGrid();
        rootGridPane.add(rectangleWithHoles,0,1);
        List<Rectangle> rectangleList=createclickable(); //calling method for hover effect
        for(Rectangle rectangle:rectangleList){
            rootGridPane.add(rectangle,0,1);

        }
    setNamesButton.setOnAction(event->{
       if(isplayeroneturn){
        playerNameLabel.setText(String.valueOf(playeArOneTextField.getText()));}
       else{
           playerNameLabel.setText(String.valueOf(playerTwoTextField.getText()));}

    }
    );

    }
    private Shape createGameStructuralGrid(){
        Shape rectangleWithHoles=new javafx.scene.shape.Rectangle((COLUMNS+1)*CIRCLE_DIAMETER,(ROWS+1)*CIRCLE_DIAMETER);
        for(int row=0;row<ROWS;row++){
            for(int col=0;col<COLUMNS;col++) {
                Circle circle = new Circle();
                circle.setRadius(CIRCLE_DIAMETER / 2);
                circle.setCenterX(CIRCLE_DIAMETER / 2);
                circle.setCenterY(CIRCLE_DIAMETER / 2);
                circle.setSmooth(true);

                circle.setTranslateX(col * (CIRCLE_DIAMETER + 5) + CIRCLE_DIAMETER / 4); //+5 for padding between circles
                circle.setTranslateY(row * (CIRCLE_DIAMETER + 5) + CIRCLE_DIAMETER / 4);
                //subtracting holes from rectangle
                rectangleWithHoles = Shape.subtract(rectangleWithHoles, circle); //subtracting holes from rectangle
            }}
        rectangleWithHoles.setFill(Color.WHITE);
        return rectangleWithHoles;
    }
//creating hover effect
    private List<Rectangle> createclickable(){
        java.util.List<Rectangle> rectangleList=new ArrayList<>();  //since we have list of rectangles
        for(int col=0;col<COLUMNS;col++){
            Rectangle rectangle=new Rectangle(CIRCLE_DIAMETER,(ROWS+1)*CIRCLE_DIAMETER);
            rectangle.setFill(Color.TRANSPARENT);
            //introducing translate motion
            rectangle.setTranslateX(col * (CIRCLE_DIAMETER + 5) +CIRCLE_DIAMETER / 4);
            //hover

           rectangle.setOnMouseEntered(event->rectangle.setFill(Color.valueOf("#eeeeee26")));
            rectangle.setOnMouseExited(event->rectangle.setFill(Color.TRANSPARENT));
            //each rectangle is clickable
            final int column=col;
            rectangle.setOnMouseClicked(event->{
                if(isallowedtoinsert) {
                    isallowedtoinsert=false; //when more than 1 disc is inserted it would not allow

                    insertDisc(new Disc(isplayeroneturn), column);
                }});
            rectangleList.add(rectangle);
        }
        return rectangleList;
    }
    //inserting disc
    private void insertDisc(Disc disc,int column){

        int row=ROWS-1;
        while(row>=0){
            if (inserteddiscarray[row][column]==null)
                break;
            row--;
            if(row<0)
                return;
        }

        inserteddiscarray[row][column]=disc; //structural change ,inserting disc object
        insertedDiscsPane.getChildren().add(disc);
        disc.setTranslateX(column * (CIRCLE_DIAMETER + 5) +CIRCLE_DIAMETER / 4);
        TranslateTransition translateTransition=new TranslateTransition(Duration.seconds(0.5),disc);
        translateTransition.setToY(row * (CIRCLE_DIAMETER + 5) + CIRCLE_DIAMETER / 4);
        int current_row=row;
        //change from player one to player 2 also change disc color
        translateTransition.setOnFinished(event->{
            isallowedtoinsert=true;
            if(gameEnded(current_row,column)){
                gameOver();
            }
            isplayeroneturn=!isplayeroneturn; //changing color
            playerNameLabel.setText(isplayeroneturn?playeArOneTextField.getText():playerTwoTextField.getText());

        });
        translateTransition.play();
    }

    private void gameOver() {

        //CHECK Who is current player
        String winner=isplayeroneturn?playeArOneTextField.getText():playerTwoTextField.getText();
        System.out.println("Winner is :"+winner);
        Alert alert=new Alert(Alert.AlertType.INFORMATION); //displaying alert dialog box
        alert.setTitle("Winner"); //setting title of alert box
        alert.setHeaderText("Winner is "+winner);  //announcing name of winner in alertbox
        //Creating buttons
        ButtonType yesbtn=new ButtonType("Yes");
        ButtonType nobtn =new ButtonType("NO,Exit");
        alert.getButtonTypes().setAll(yesbtn,nobtn);
        //In order to display result after animation is complete
        Platform.runLater(()->{
            Optional<ButtonType>btnClicked=alert.showAndWait();
            //clicking yes button will reset game
            if(btnClicked.isPresent() && btnClicked.get()==yesbtn){
                resetGame();
            } //clicking no button will exit game
            else{
                Platform.exit();
                System.exit(0);
            }
        });
    }

    public void resetGame()
    { 
        //this methode will set game to initial stage
        insertedDiscsPane.getChildren().clear(); //clear all inserted discs from pane
        //clear discs from stucture
        for(int row=0;row< inserteddiscarray.length;row++){
            for(int col=0;col<inserteddiscarray[row].length;col++){
                inserteddiscarray[row][col]=null; //setting null that is removing discs
            }
        }
        isplayeroneturn=true; //since player one will start the game
        playerNameLabel.setText(playeArOneTextField.getText());
        createplayground(); //prepare new playground
    }

    //DECIDE WHEN GAME IS ENDED
    private boolean gameEnded(int row,int column){
        //for vertical Points
        //in vertical to make a match of 4,we have to compare the element
       List<Point2D>verticalPoints= IntStream.rangeClosed(row-3,row+3)
                //with above 3 and below 3 elements
                .mapToObj(r->new Point2D(r,column)) //Point2D class has (x,y) coordinates
                .collect(Collectors.toList());

        //for horizontal Points
        //in vertical to make a match of 4,we have to compare the element
        //with above 3 and below 3 elements
        List<Point2D>horizontalPoints= IntStream.rangeClosed(column-3,column+3)
                .mapToObj(c->new Point2D(row,c)) //Point2D class has (x,y) coordinates
                .collect(Collectors.toList());


        Point2D startPoint1=new Point2D(row-3,column+3);
        List<Point2D> diagonal1Points=IntStream.rangeClosed(0,6).mapToObj(i->(startPoint1.add(i,-i))).collect(Collectors.toList());

        Point2D startPoint2=new Point2D(row-3,column-3);
        List<Point2D> diagonal2Points=IntStream.rangeClosed(0,6).mapToObj(i->(startPoint2.add(i,i))).collect(Collectors.toList());
//check if game is ended
        boolean isEnded=checkCombinations(verticalPoints)||checkCombinations(horizontalPoints)||checkCombinations(diagonal1Points)
                ||checkCombinations(diagonal2Points);
        return isEnded;

    }
    //checking if 4 discs of same color are connected
    private  boolean checkCombinations(List<Point2D> points){
        int chain=0;
        for(Point2D point:points){
            int rowIndexForArray= (int) point.getX();
            int columnIndexForArray= (int) point.getY();
            Disc disc=getDiscifPresent(rowIndexForArray,columnIndexForArray);
            if(disc!=null && disc.isPlayerOneMove==isplayeroneturn) {
                chain++;
                if (chain == 4)
                    return true;
            }
            else
                chain=0;

        }
        return false;
    }

    private Disc getDiscifPresent(int row,int column){
        if(row>=ROWS || row<0||column>=COLUMNS|| column<0)
            return null;
        return inserteddiscarray[row][column];

    }
    private  static class Disc extends Circle{
        private final boolean isPlayerOneMove;

        public Disc(boolean isPlayerOneMove){
            this.isPlayerOneMove=isPlayerOneMove;
            setRadius(CIRCLE_DIAMETER/2);
            setFill(isPlayerOneMove?Color.valueOf(discColor1):Color.valueOf(discColor2));
            setCenterX(CIRCLE_DIAMETER/2);
            setCenterY(CIRCLE_DIAMETER/2);
        }
    }
    @Override
    public void initialize(URL url, ResourceBundle resourceBundle) {
        loadbtn.setOnAction(event -> loadscene2());
    }
    private void loadscene2() {
        try{
            FXMLLoader loader=new FXMLLoader(getClass().getResource("sample1.fxml"));
            GridPane root=loader.load();
            //get controller of scene2
            Controller1 controller1=loader.getController();
            controller1.createplayground();
            Stage stage=new Stage();
            stage.setScene(new Scene(root));
            stage.setTitle("Second window");
            stage.show();
        }
        catch(IOException ex){
            System.out.println("akjwn");
        }
    }
}





==========================================================================================================================================================================
Controller1.java
package com.connect4games;

import javafx.animation.TranslateTransition;
import javafx.application.Platform;
import javafx.fxml.FXML;
import javafx.fxml.Initializable;
import javafx.geometry.Point2D;
import javafx.scene.control.*;
import javafx.scene.layout.GridPane;
import javafx.scene.layout.Pane;
import javafx.scene.paint.Color;
import javafx.scene.shape.Circle;
import javafx.scene.shape.Rectangle;
import javafx.scene.shape.Shape;
import javafx.util.Duration;
import java.util.concurrent.ThreadLocalRandom;
import java.net.URL;
import java.util.*;
import java.util.stream.Collectors;
import java.util.stream.IntStream;
import javafx.animation.Timeline;
public class Controller1 implements Initializable {

    private static  final int COLUMNS=7;
    private static  final int ROWS=6;
    private static  final int CIRCLE_DIAMETER=80;
    private static  final String discColor1="#24303E";
    private static  final String discColor2="#4CAA88";
    private  boolean isplayeroneturn =true;
    private boolean isallowedtoinsert=true;
    private Disc[][] inserteddiscarray=new Disc[ROWS][COLUMNS];
    @FXML
    GridPane rootGridPane;
    @FXML
    Pane insertedDiscsPane;
    @FXML
    Label playerNameLabel;
    @FXML
    public  TextField playeArOneTextField,playerTwoTextField;
    @FXML
    Button setNamesButton;
    public Label moves1,moves2; //Represent number of moves left
    public TextField p1text,p2text; //player names
    public MenuItem newgame;
    public MenuItem info;


//method to get random values within an interval
    public int getRandomvalue(int min,int max){
        return ThreadLocalRandom.current().nextInt(min, max+1);
    }
    int min=12;
    int max=40;
    int move1_val=getRandomvalue(min,max);  //player1 moves
    int  move2_val=getRandomvalue(min,max); //player 2 moves

    public  void createplayground(){
        //Rectangle is pre-defined java class in javafx
        Shape rectangleWithHoles=createGameStructuralGrid();
        rootGridPane.add(rectangleWithHoles,0,1);
        List<Rectangle> rectangleList=createclickable(); //calling method for hover effect
        for(Rectangle rectangle:rectangleList){
            rootGridPane.add(rectangle,0,1);

        }
        setNamesButton.setOnAction(event->{
            if(isplayeroneturn){
                playerNameLabel.setText(String.valueOf(playeArOneTextField.getText()));}
            else{
                playerNameLabel.setText(String.valueOf(playerTwoTextField.getText()));}
            p1text.setText(String.valueOf(playeArOneTextField.getText()+" moves"));
            p2text.setText(String.valueOf(playerTwoTextField.getText()+" moves"));
        });





    }
    private Shape createGameStructuralGrid(){

        Shape rectangleWithHoles=new javafx.scene.shape.Rectangle((COLUMNS+1)*CIRCLE_DIAMETER,(ROWS+1)*CIRCLE_DIAMETER);
        for(int row=0;row<ROWS;row++){
            for(int col=0;col<COLUMNS;col++) {
                Circle circle = new Circle();
                circle.setRadius(CIRCLE_DIAMETER / 2);
                circle.setCenterX(CIRCLE_DIAMETER / 2);
                circle.setCenterY(CIRCLE_DIAMETER / 2);
                circle.setSmooth(true);

                circle.setTranslateX(col * (CIRCLE_DIAMETER + 5) + CIRCLE_DIAMETER / 4); //+5 for padding between circles
                circle.setTranslateY(row * (CIRCLE_DIAMETER + 5) + CIRCLE_DIAMETER / 4);
                //subtracting holes from rectangle
                rectangleWithHoles = Shape.subtract(rectangleWithHoles, circle); //subtracting holes from rectangle
            }}
        rectangleWithHoles.setFill(Color.WHITE);
        return rectangleWithHoles;
    }
    //creating hover effect
    private List<Rectangle> createclickable(){
        java.util.List<Rectangle> rectangleList=new ArrayList<>();  //since we have list of rectangles
        for(int col=0;col<COLUMNS;col++){
            Rectangle rectangle=new Rectangle(CIRCLE_DIAMETER,(ROWS+1)*CIRCLE_DIAMETER);
            rectangle.setFill(Color.TRANSPARENT);
            //introducing translate motion
            rectangle.setTranslateX(col * (CIRCLE_DIAMETER + 5) +CIRCLE_DIAMETER / 4);
            //hover

            rectangle.setOnMouseEntered(event->rectangle.setFill(Color.valueOf("#eeeeee26")));
            rectangle.setOnMouseExited(event->rectangle.setFill(Color.TRANSPARENT));
            //each rectangle is clickable
            final int column=col;
            rectangle.setOnMouseClicked(event->{
                //decreasing number of moves on each click
                if(isplayeroneturn){
                    move1_val--;
                    moves1.setText(String.valueOf(move1_val));
                    if(move1_val<=0){
                        moveOver();
                    }
                }
                else{
                    move2_val--;
                    moves2.setText(String.valueOf(move2_val));
                    if(move2_val<=0){
                        moveOver();
                    }
                }
                if(isallowedtoinsert) {
                    //when more than 1 disc is inserted it would not allow
                    isallowedtoinsert=false;
                    insertDisc(new Disc(isplayeroneturn), column);
                }

            });
            rectangleList.add(rectangle);
        }
        return rectangleList;
    }
    //inserting disc
    private void insertDisc(Disc disc,int column){

        int row=ROWS-1;
        while(row>=0){
            if (inserteddiscarray[row][column]==null)
                break;
            row--;
            if(row<0)
                return;
        }

        inserteddiscarray[row][column]=disc; //structural change ,inserting disc object
        insertedDiscsPane.getChildren().add(disc);
        disc.setTranslateX(column * (CIRCLE_DIAMETER + 5) +CIRCLE_DIAMETER / 4);
        TranslateTransition translateTransition=new TranslateTransition(Duration.seconds(0.5),disc);
        translateTransition.setToY(row * (CIRCLE_DIAMETER + 5) + CIRCLE_DIAMETER / 4);
        int current_row=row;
        //change from player one to player 2 also change disc color
        translateTransition.setOnFinished(event->{
            isallowedtoinsert=true;
            if(gameEnded(current_row,column)){
                gameOver();
            }
            isplayeroneturn=!isplayeroneturn; //changing color
            playerNameLabel.setText(isplayeroneturn?playeArOneTextField.getText():playerTwoTextField.getText());

        });
        translateTransition.play();
    }

    private void gameOver() {
        //CHECK Who is current player
        String winner=isplayeroneturn?playeArOneTextField.getText():playerTwoTextField.getText();
        System.out.println("Winner is :"+winner);
        Alert alert=new Alert(Alert.AlertType.INFORMATION); //displaying alert dialog box
        alert.setTitle("Winner"); //setting title of alert box
        alert.setHeaderText("Winner is "+winner);  //announcing name of winner in alertbox
        //Creating buttons
        ButtonType yesbtn=new ButtonType("Yes");
        ButtonType nobtn =new ButtonType("NO,Exit");
        alert.getButtonTypes().setAll(yesbtn,nobtn);
        //In order to display result after animation is complete
        Platform.runLater(()->{
            Optional<ButtonType>btnClicked=alert.showAndWait();
            //clicking yes button will reset game
            if(btnClicked.isPresent() && btnClicked.get()==yesbtn){
                resetGame();
                moves1.setText(String.valueOf(move1_val));
                moves2.setText(String.valueOf(move2_val));
            } //clicking no button will exit game
            else{
                Platform.exit();
                System.exit(0);
            }
        });
    }

    public void resetGame()
    {
        //this methode will set game to initial stage
        insertedDiscsPane.getChildren().clear(); //clear all inserted discs from pane
        //clear discs from stucture
        for(int row=0;row< inserteddiscarray.length;row++){
            for(int col=0;col<inserteddiscarray[row].length;col++){
                inserteddiscarray[row][col]=null; //setting null that is removing discs
            }
        }
        //reset number of moves for each player
        int min=15;
        int max=45;
        int move1_val=getRandomvalue(min,max);
        int  move2_val=getRandomvalue(min,max);
        moves1.setText(String.valueOf(move1_val));
        moves2.setText(String.valueOf(move2_val));
        isplayeroneturn=true; //since player one will start the game
        playerNameLabel.setText(playeArOneTextField.getText());

        createplayground(); //prepare new playground
    }
    //method to check if number of moves exceed
   private void moveOver(){
      Alert alert=new Alert(Alert.AlertType.INFORMATION);
      alert.setTitle("Moves Exceed");
      alert.setHeaderText("No moves left");

       ButtonType yesbtn=new ButtonType("Yes");
       ButtonType nobtn =new ButtonType("NO,Exit");
       alert.getButtonTypes().setAll(yesbtn,nobtn);
       //In order to display result after animation is complete
       Platform.runLater(()->{
           Optional<ButtonType>btnClicked=alert.showAndWait();
           //clicking yes button will reset game
           if(btnClicked.isPresent() && btnClicked.get()==yesbtn){
               resetGame();
           } //clicking no button will exit game
           else{
               Platform.exit();
               System.exit(0);
           }
       });
   }

    //DECIDE WHEN GAME IS ENDED
    private boolean gameEnded(int row,int column){
        //for vertical Points
        //in vertical to make a match of 4,we have to compare the element
        List<Point2D>verticalPoints= IntStream.rangeClosed(row-3,row+3)
                //with above 3 and below 3 elements
                .mapToObj(r->new Point2D(r,column)) //Point2D class has (x,y) coordinates
                .collect(Collectors.toList());

        //for horizontal Points
        //in vertical to make a match of 4,we have to compare the element
        //with above 3 and below 3 elements
        List<Point2D>horizontalPoints= IntStream.rangeClosed(column-3,column+3)
                .mapToObj(c->new Point2D(row,c)) //Point2D class has (x,y) coordinates
                .collect(Collectors.toList());


        Point2D startPoint1=new Point2D(row-3,column+3);
        List<Point2D> diagonal1Points=IntStream.rangeClosed(0,6).mapToObj(i->(startPoint1.add(i,-i))).collect(Collectors.toList());

        Point2D startPoint2=new Point2D(row-3,column-3);
        List<Point2D> diagonal2Points=IntStream.rangeClosed(0,6).mapToObj(i->(startPoint2.add(i,i))).collect(Collectors.toList());
//check if game is ended
        boolean isEnded=checkCombinations(verticalPoints)||checkCombinations(horizontalPoints)||checkCombinations(diagonal1Points)
                ||checkCombinations(diagonal2Points);
        return isEnded;

    }
    //checking if 4 discs of same color are connected
    private  boolean checkCombinations(List<Point2D> points){
        int chain=0;
        for(Point2D point:points){
            int rowIndexForArray= (int) point.getX();
            int columnIndexForArray= (int) point.getY();
            Disc disc=getDiscifPresent(rowIndexForArray,columnIndexForArray);
            if(disc!=null && disc.isPlayerOneMove==isplayeroneturn) {
                chain++;
                if (chain == 4)
                    return true;
            }
            else
                chain=0;

        }
        return false;
    }

    private Disc getDiscifPresent(int row,int column){
        if(row>=ROWS || row<0||column>=COLUMNS|| column<0)
            return null;
        return inserteddiscarray[row][column];

    }

    private  static class Disc extends Circle{
        private final boolean isPlayerOneMove;

        public Disc(boolean isPlayerOneMove){
            this.isPlayerOneMove=isPlayerOneMove;
            setRadius(CIRCLE_DIAMETER/2);
            setFill(isPlayerOneMove?Color.valueOf(discColor1):Color.valueOf(discColor2));
            setCenterX(CIRCLE_DIAMETER/2);
            setCenterY(CIRCLE_DIAMETER/2);

        }
    }
public void info(){
    Alert alert=new Alert(Alert.AlertType.INFORMATION);
    alert.setTitle("Game rules");
    alert.setHeaderText("The following is stage 2 for this game.The moves for each player are restricted and assigned randomly." +
            "Finish the game within the given moves.");
    alert.show();
}
    @Override
    public void initialize(URL url, ResourceBundle resourceBundle) {
newgame.setOnAction(event -> resetGame());
info.setOnAction(event -> info());
    }



}

