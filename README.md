# JavaFx
package application;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import javafx.application.Application;
import javafx.beans.value.ChangeListener;
import javafx.beans.value.ObservableValue;
import javafx.event.ActionEvent;
import javafx.event.EventHandler;
import javafx.geometry.Insets;
import javafx.stage.Stage;
import javafx.scene.Group;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.control.RadioButton;
import javafx.scene.control.TextField;
import javafx.scene.control.Toggle;
import javafx.scene.control.ToggleGroup;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;


public class Main extends Application {
	@Override
	public void start(Stage primaryStage) 
	{		
		primaryStage.setTitle("JAva Box GUI");
		Group root = new Group();					
				
		//Radio Buttonlar 
		VBox vbox = new VBox();
		//Text işlemleri
		VBox vbox1 = new VBox();
		//HBox(3) Ana ekran 	
		HBox hbox3 = new HBox(25);
		
		
		//HBOx(1)
		HBox hbox1 =new HBox(15);	
		Label etiket_1= new Label("1.Sayi:");	
		TextField sayi_1= new TextField();
		sayi_1.setPromptText("Sayi Gir");
		sayi_1.relocate(15, 15);
		hbox1.getChildren().addAll(etiket_1,sayi_1);
		
		//Sadece Numara Aldırmayı Sağlıyorum
		sayi_1.textProperty().addListener(new ChangeListener<String>() {

			@Override
			public void changed(ObservableValue<? extends String> arg0, String oldValue, String newValue) {
				try{
					if(sayi_1.getText().length() != 0){
						if(sayi_1.getText().startsWith("-") && newValue.substring(1,newValue.length()).length() > 0){
							Integer.parseInt(newValue.substring(1,newValue.length()));
						}else if(!sayi_1.getText().startsWith("-")){
							Integer.parseInt(newValue);
						}
					}
				}catch (Exception e) {
					sayi_1.setText(oldValue);
				}
				
			}
		});		
				
		//HBox(2)
		HBox hbox2 = new HBox(15);
		Label etiket_2 = new Label("2.Sayi:");
		TextField sayi_2= new TextField();
		sayi_2.setPromptText("Sayi Gir");
		Button hesapla = new Button("Hesapla");
		
		hbox2.getChildren().addAll(etiket_2,sayi_2,hesapla);
		
		sayi_2.textProperty().addListener(new ChangeListener<String>() {

			@Override
			public void changed(ObservableValue<? extends String> arg0, String oldValue, String newValue) {
				
				try{
					
					if(sayi_2.getText().length() != 0)
					{
						if(sayi_2.getText().startsWith("-") && newValue.substring(1,newValue.length()).length() > 0)
						{
							Integer.parseInt(newValue.substring(1, newValue.length()));
						}
						else if (!sayi_2.getText().startsWith("-"))
						{
							Integer.parseInt(newValue);
						}
					}					
				}catch (Exception e) {
					
					sayi_2.setText(oldValue);
				}
				
			}
		});
		
		
		//Vbox(2)		
			vbox1.getChildren().addAll(hbox1,hbox2);
			
		//VBox(1)
		final ToggleGroup grup = new ToggleGroup();
		
		RadioButton add = new RadioButton("Add");
		add.setSelected(true);
		add.setToggleGroup(grup);		
				
		RadioButton sub = new RadioButton("Sub");
		sub.setToggleGroup(grup);
		
		
		RadioButton multiply = new RadioButton("Multiply");
		multiply.setToggleGroup(grup);
			
		RadioButton devide = new RadioButton("Devide");
		devide.setToggleGroup(grup);		
		
		RadioButton mod = new RadioButton("Mod");
		mod.setToggleGroup(grup);
		
		RadioButton factorial = new RadioButton("Factorial");
		factorial.setToggleGroup(grup);
		factorial.setOnAction(new EventHandler<ActionEvent>() {
			
			@Override
			public void handle(ActionEvent event) 
			{
				sayi_2.clear();
				if(!sayi_2.isDisable()){
					sayi_2.setDisable(true);
				}
				
			}
		});
		
		RadioButton fibonacci = new RadioButton("Fibonacci");
		fibonacci.setToggleGroup(grup);
		
		//Çok Önemli
		grup.selectedToggleProperty().addListener(new ChangeListener<Toggle>() {
			@Override
			public void changed(ObservableValue<? extends Toggle> arg0, Toggle oldValue, Toggle newValue) {
				
				if(((RadioButton) newValue).getText().equals("Factorial") || ((RadioButton) newValue).getText().equals("Fibonacci")){
					sayi_2.clear();
					if(!sayi_2.isDisable()){
						sayi_2.setDisable(true);
					}
				}else{
					if(sayi_2.isDisable()){
						sayi_2.setDisable(false);
					}
				}
			}
			
		});
		
		hesapla.setOnAction(new EventHandler<ActionEvent>() {
			
			@Override
			public void handle(ActionEvent arg0) {
				
				if(sayi_1.getText().isEmpty())				
					Uyari.display("Uyari","Lütfen 1.Sayi Alanını Doldurunuz.");		
				else if (sayi_2.getText().isEmpty() && !factorial.isSelected() && !fibonacci.isSelected())
					Uyari.display("Uyari","Lütfen 2.Sayi Alanını Doldurunuz.");
				
				else if(!sayi_1.getText().isEmpty() && Integer.parseInt(sayi_1.getText()) < 0 && (fibonacci.isSelected() || factorial.isSelected())){
					Uyari.display("Uyari","Pozitif Sayi Giriniz!");	
				}					
				else if(!sayi_2.getText().isEmpty() && Integer.parseInt(sayi_2.getText()) == 0 && devide.isSelected()){
					Uyari.display("Uyari","Lütfen Paydaya 0 Girmeyin");	
				}				
				else{
					try{
						String operation = null;					
						if(add.isSelected())						
							operation="add";						
						else if(sub.isSelected())
							operation="sub";
						else if (multiply.isSelected())
							operation="multiply";
						else if(devide.isSelected())
							operation="devide";
						else if (mod.isSelected())
							operation="mod";
						else if(fibonacci.isSelected())
							operation="fibonacci";
						else if (factorial.isSelected())
							operation="factorial";
						
						String urlString = "http://localhost:8090/HelloWolrd/restful/Hesapla/"+operation+"/"+sayi_1.getText();
						if(!sayi_2.getText().isEmpty())
							urlString = urlString + "/"+sayi_2.getText();
						URL url = new URL(urlString);
						HttpURLConnection conn = (HttpURLConnection) url.openConnection();
						conn.setRequestMethod("GET");
						conn.setRequestProperty("Accept", "text/plain");
						if(conn.getResponseCode() != 200)
						{
							throw new RuntimeException("Failed:HTTP eror code: "+conn.getResponseCode());
						}
						
						BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream()));
						String output;
						System.out.println("Output Server ......\n");
						while((output=br.readLine()) != null)
						{
							System.out.println(output);
						}
						
						conn.disconnect();
						
					}catch (Exception e) {
						System.out.println(e);
					}
				
				}
			}	
		});
			
		
		
		//VBox(2)
			vbox.getChildren().addAll(add,sub,multiply,devide,mod,factorial,fibonacci);
		
		//HBox(3)
			hbox3.getChildren().addAll(vbox,vbox1);
			hbox3.setPadding(new Insets(35, 35, 35, 35));
		Scene scene = new Scene(hbox3,450,300);		
		
		primaryStage.setScene(scene);
		primaryStage.show();		
	}
	
	public static void main(String[] args) {
		launch(args);
	}
}

//Uyarı Pencerisi için 
package application;

import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.layout.VBox;
import javafx.stage.Modality;
import javafx.stage.Stage;

public class Uyari {

	public static void display(String title , String message)
	{
		
		Stage window = new Stage();
		window.initModality(Modality.NONE);
		window.setTitle(title);
		window.setMinWidth(350);
		
		Label uyari = new Label();
		uyari.setText(message);
		
		Button closeButton = new Button("Kapat");
		closeButton.setOnAction(event ->window.close());
		
		VBox layout = new VBox(35);
		layout.getChildren().addAll(uyari,closeButton);
		layout.setAlignment(Pos.CENTER);
		
		Scene scene = new Scene(layout);
		window.setScene(scene);
		window.showAndWait();
		
	}	
}
