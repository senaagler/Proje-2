# Proje-2
Card Game
package proje;

import javafx.application.Application;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

import java.util.ArrayList;
import java.util.List;
import java.util.Random;



public class proje extends Application {
    private List<SavasAraci> oyuncuSecimHavuzu = new ArrayList<>();
    private List<SavasAraci> bilgisayarSecimHavuzu = new ArrayList<>();
    private List<SavasAraci> oyuncuKartlari = new ArrayList<>();
    private List<SavasAraci> bilgisayarKartlari = new ArrayList<>();
    private Label oyuncuSkorLabel = new Label("Oyuncu Skoru: 0");
    private Label bilgisayarSkorLabel = new Label("Bilgisayar Skoru: 0");
    private int oyuncuSkor = 0;
    private int bilgisayarSkor = 0;
    private int turSayisi = 1;
    private int toplamHamle = 0;
    private final int MAX_HAMLE = 5;
    private Random random = new Random();
    
    
    

    @Override
    public void start(Stage primaryStage) {
        
        VBox startScreen = new VBox(20);
        startScreen.setStyle("-fx-padding: 20; -fx-alignment: center; -fx-background-color: #a6c8ff; -fx-background-radius: 20px;");

        Label titleLabel = new Label("Savaş Oyunu");
        titleLabel.setStyle("-fx-font-size: 40px; -fx-font-weight: bold; -fx-text-fill: #2c3e50;");

        Button startButton = new Button("Oyuna Başla");
        startButton.setStyle("-fx-background-color: #2980b9; -fx-text-fill: white; -fx-font-size: 18px; -fx-padding: 15; -fx-border-radius: 20px; -fx-cursor: hand;");
        startButton.setOnAction(e -> startGame(primaryStage));

        startButton.setOnMouseEntered(e -> startButton.setStyle("-fx-background-color: #3498db; -fx-text-fill: white; -fx-font-size: 18px; -fx-padding: 15; -fx-border-radius: 20px; -fx-cursor: hand;"));
        startButton.setOnMouseExited(e -> startButton.setStyle("-fx-background-color: #2980b9; -fx-text-fill: white; -fx-font-size: 18px; -fx-padding: 15; -fx-border-radius: 20px; -fx-cursor: hand;"));

        startScreen.getChildren().addAll(titleLabel, startButton);

        Scene startScene = new Scene(startScreen, 800, 500);
        primaryStage.setTitle("Savaş Oyunu - Başlangıç");
        primaryStage.setScene(startScene);
        primaryStage.show();
    }

    private void startGame(Stage primaryStage) {
    	
        
        oyuncuSkorLabel.setStyle("-fx-font-size: 18px; -fx-font-weight: bold; -fx-padding: 10; -fx-background-color: #ffffff; -fx-border-color: #3498db; -fx-border-radius: 10;");
        bilgisayarSkorLabel.setStyle("-fx-font-size: 18px; -fx-font-weight: bold; -fx-padding: 10; -fx-background-color: #ffffff; -fx-border-color: #3498db; -fx-border-radius: 10;");

        
        oyuncuSecimHavuzu.addAll(List.of(new Ucak(), new Obus(), new Firkateyn()));
        bilgisayarSecimHavuzu.addAll(List.of(new Ucak(), new Obus(), new Firkateyn()));

        VBox root = new VBox(20);
        root.setStyle("-fx-padding: 20; -fx-alignment: center; -fx-background-color: #f5f5f5;");

        Label oyuncuKartLabel = new Label("Oyuncunun Kartları: Henüz Seçilmedi");
        Button saldirButton = new Button("Saldır");
        saldirButton.setDisable(true); // Kart seçimi tamamlanınca başlıycak
        saldirButton.setStyle("-fx-background-color: #e74c3c; -fx-text-fill: white; -fx-font-size: 20px; -fx-padding: 15; -fx-border-radius: 10px;");

       
        
        HBox secimAlanı = new HBox(15); 
        // Kartlar daha sık görünsün diye spacing kısmını arttırdık.
        secimAlanı.setStyle("-fx-alignment: center;");

        root.getChildren().addAll(
                oyuncuSkorLabel,
                bilgisayarSkorLabel,
                oyuncuKartLabel,
                secimAlanı,
                saldirButton
        );

        
        kartSecimGoster(secimAlanı, oyuncuKartLabel, saldirButton);

        // Saldırma İşlemini burda gösterdik.
        saldirButton.setOnAction(e -> kartSecVeSaldir(oyuncuKartLabel, saldirButton, secimAlanı));

        
        Scene scene = new Scene(root, 800, 700);
        primaryStage.setScene(scene);
        primaryStage.setTitle("Savaş Oyunu");
        primaryStage.show();
    }

    private void kartSecimGoster(HBox secimAlanı, Label oyuncuKartLabel, Button saldirButton) {
        secimAlanı.getChildren().clear(); // Mevcut seçim alanı temizleniyor

        List<SavasAraci> benzersizKartlar = new ArrayList<>();
        for (SavasAraci kart : oyuncuSecimHavuzu) {
            if (benzersizKartlar.stream().noneMatch(k -> k.sinifAdi.equals(kart.sinifAdi))) {
                benzersizKartlar.add(kart);
            }
        }

        if (turSayisi == 1) {
            
            for (SavasAraci kart : benzersizKartlar) {
                Button kartButton = new Button();
                ImageView kartGorsel = new ImageView(new Image(kart.getImagePath()));
                kartGorsel.setFitWidth(180); 
                kartGorsel.setFitHeight(180);
                kartGorsel.setPreserveRatio(true);
                kartButton.setGraphic(kartGorsel);
                kartButton.setOnAction(e -> {
                    if (oyuncuKartlari.size() < 3) {
                        
                        oyuncuKartlari.add(kart);
                        oyuncuKartLabel.setText("Oyuncunun Kartları: " +
                                oyuncuKartlari.stream().map(k -> k.sinifAdi).reduce((a, b) -> a + ", " + b).orElse(""));

                        if (oyuncuKartlari.size() == 3) {
                            saldirButton.setDisable(false); 
                            bilgisayarKartSec(); 
                        }
                    }
                });

                kartButton.setStyle("-fx-background-color: transparent; -fx-border-color: transparent;");
                kartButton.setOnMouseEntered(e -> kartButton.setStyle("-fx-background-color: #d5dbdb;"));
                kartButton.setOnMouseExited(e -> kartButton.setStyle("-fx-background-color: transparent;"));

                secimAlanı.getChildren().add(kartButton); // Kart butonlarını burada ekledik.
            }
        } else {
            // Sonraki tur: Oyuncuya 1 rastgele kart verilip, 2 kartı kendisi seçecegi sekilde yaptık.
            if (oyuncuKartlari.size() < 1) {
                SavasAraci rastgeleKart = oyuncuSecimHavuzu.get(random.nextInt(oyuncuSecimHavuzu.size()));
                oyuncuKartlari.add(rastgeleKart);

                oyuncuKartLabel.setText("Oyuncunun Rastgele Kartı: " + rastgeleKart.sinifAdi);
                ImageView kartGorsel = new ImageView(new Image(rastgeleKart.getImagePath()));
                kartGorsel.setFitWidth(180);
                kartGorsel.setFitHeight(180);
                kartGorsel.setPreserveRatio(true);
                secimAlanı.getChildren().add(kartGorsel);
            }

            // Geriye kalan 2 kart için seçim yapma kısmı
            for (SavasAraci kart : benzersizKartlar) {
                Button kartButton = new Button();
                ImageView kartGorsel = new ImageView(new Image(kart.getImagePath()));
                kartGorsel.setFitWidth(180);
                kartGorsel.setFitHeight(180);
                kartGorsel.setPreserveRatio(true);
                kartButton.setGraphic(kartGorsel);
                kartButton.setOnAction(e -> {
                    if (oyuncuKartlari.size() < 3) {
                        oyuncuKartlari.add(kart);
                        oyuncuKartLabel.setText("Oyuncunun Kartları: " +
                                oyuncuKartlari.stream().map(k -> k.sinifAdi).reduce((a, b) -> a + ", " + b).orElse(""));

                        if (oyuncuKartlari.size() == 3) {
                            saldirButton.setDisable(false);
                            bilgisayarKartSec();
                        }
                    }
                });

                kartButton.setStyle("-fx-background-color: transparent; -fx-border-color: transparent;");
                kartButton.setOnMouseEntered(e -> kartButton.setStyle("-fx-background-color: #d5dbdb;"));
                kartButton.setOnMouseExited(e -> kartButton.setStyle("-fx-background-color: transparent;"));

                secimAlanı.getChildren().add(kartButton);
            }
        }
    }

    private void bilgisayarKartSec() {
        bilgisayarKartlari.clear();
        while (bilgisayarKartlari.size() < 3) {
            SavasAraci rastgeleKart = bilgisayarSecimHavuzu.get(random.nextInt(bilgisayarSecimHavuzu.size()));
            if (!bilgisayarKartlari.contains(rastgeleKart)) {
                bilgisayarKartlari.add(rastgeleKart);
            }
        }
    }

    private void kartSecVeSaldir(Label oyuncuKartLabel, Button saldirButton, HBox secimAlanı) {
        toplamHamle++;
        turSayisi++; 

        int oyuncuToplamHasar = 0;
        int bilgisayarToplamHasar = 0;

        
        for (int i = 0; i < 3; i++) {
            SavasAraci oyuncuKart = oyuncuKartlari.get(i);
            SavasAraci bilgisayarKart = bilgisayarKartlari.get(i);

            
            
            int oyuncuHasar = oyuncuKart.avantajVurusGucu(bilgisayarKart.sinifAdi) - bilgisayarKart.dayaniklilik;
            int bilgisayarHasar = bilgisayarKart.avantajVurusGucu(oyuncuKart.sinifAdi) - oyuncuKart.dayaniklilik;

           
            
            if (oyuncuHasar > bilgisayarHasar) {
                oyuncuToplamHasar += 10;
            } else if (bilgisayarHasar > oyuncuHasar) {
                bilgisayarToplamHasar += 10;
            }
        }

        
        
        oyuncuSkor += oyuncuToplamHasar;
        bilgisayarSkor += bilgisayarToplamHasar;

        oyuncuSkorLabel.setText("Oyuncu Skoru: " + oyuncuSkor);
        bilgisayarSkorLabel.setText("Bilgisayar Skoru: " + bilgisayarSkor);

        // Yeni Kartların Açılması için olan kısım
        if (oyuncuSkor >= 20 || bilgisayarSkor >= 20) {
            if (!oyuncuSecimHavuzu.contains(new Siha())) {
                oyuncuSecimHavuzu.addAll(List.of(new Siha(), new Sida(), new KFS()));
                bilgisayarSecimHavuzu.addAll(List.of(new Siha(), new Sida(), new KFS()));
            }
        }

       
        
        if (toplamHamle >= MAX_HAMLE) {
            oyunSonu();
            return;
        }

        // Yeni Tur: Oyuncunun kartları sıfırlanıcaktır.
        oyuncuKartlari.clear();

        
        SavasAraci rastgeleKart = oyuncuSecimHavuzu.get(random.nextInt(oyuncuSecimHavuzu.size()));
        oyuncuKartlari.add(rastgeleKart);
        oyuncuKartLabel.setText("Oyuncunun Rastgele Kartı: " + rastgeleKart.sinifAdi);

        
        
        saldirButton.setDisable(true);
        kartSecimGoster(secimAlanı, oyuncuKartLabel, saldirButton);
    }
    
    

    private void oyunSonu() {
        String sonuc = oyuncuSkor > bilgisayarSkor ? "Oyuncu Kazandı!" :
                (bilgisayarSkor > oyuncuSkor ? "Bilgisayar Kazandı!" : "Beraberlik!");

        VBox endScreen = new VBox(20);
        endScreen.setStyle("-fx-padding: 20; -fx-alignment: center; -fx-background-color: #f0f0f0;");

        Label endLabel = new Label(sonuc);
        endLabel.setStyle("-fx-font-size: 24px; -fx-font-weight: bold; -fx-text-fill: #2c3e50;");

        if (oyuncuSkor > bilgisayarSkor) {
        	
            // Kazanan oyuncu ise kupa resmi gösterilecek
            ImageView kupaResmi = new ImageView(new Image(getClass().getResource("/resim/kupa.jpg").toExternalForm()));
            kupaResmi.setFitWidth(150);
            kupaResmi.setFitHeight(150);
            endScreen.getChildren().add(kupaResmi);
            
        } else if (bilgisayarSkor > oyuncuSkor) {
            // Bilgisayar kazandıysa bilgisayar resmi gösterilecek
            ImageView bilgisayarResmi = new ImageView(new Image(getClass().getResource("/resim/bilgisayar.jpg").toExternalForm()));
            bilgisayarResmi.setFitWidth(150);
            bilgisayarResmi.setFitHeight(150);
            endScreen.getChildren().add(bilgisayarResmi);
        } else {
            // Beraberlik durumu
            ImageView beraberlikResmi = new ImageView(new Image(getClass().getResource("/resim/beraberlik.jpg").toExternalForm()));
            beraberlikResmi.setFitWidth(150);
            beraberlikResmi.setFitHeight(150);
            endScreen.getChildren().add(beraberlikResmi);
        }

        Button restartButton = new Button("Yeniden Başla");
        restartButton.setStyle("-fx-background-color: #2980b9; -fx-text-fill: white; -fx-font-size: 18px; -fx-padding: 10; -fx-border-radius: 5;");
        restartButton.setOnAction(e -> restartGame());

        endScreen.getChildren().addAll(endLabel, restartButton);

        Scene endScene = new Scene(endScreen, 600, 400);
        Stage endStage = new Stage();
        endStage.setScene(endScene);
        endStage.setTitle("Oyun Sonu");
        endStage.show();
    }

    private void restartGame() {
        // Skorları sıfırlıyoruz
        oyuncuSkor = 0;
        bilgisayarSkor = 0;
        oyuncuKartlari.clear();
        bilgisayarKartlari.clear();
        oyuncuSkorLabel.setText("Oyuncu Skoru: 0");
        bilgisayarSkorLabel.setText("Bilgisayar Skoru: 0");
        toplamHamle = 0;
        turSayisi = 1;

       
        
        oyuncuSecimHavuzu.clear();
        bilgisayarSecimHavuzu.clear();
        oyuncuSecimHavuzu.addAll(List.of(new Ucak(), new Obus(), new Firkateyn()));
        bilgisayarSecimHavuzu.addAll(List.of(new Ucak(), new Obus(), new Firkateyn()));

       
        Stage primaryStage = new Stage();
        start(primaryStage);
    }

    
    public static void main(String[] args) {
        launch(args);
    }
}





// Kart Sınıfı
class Kart {
    private String ad;
    private int dayaniklilik;
    private int vurusGucu;

    public Kart(String ad, int dayaniklilik, int vurusGucu) {
        this.ad = ad;
        this.dayaniklilik = dayaniklilik;
        this.vurusGucu = vurusGucu;
    }

    public String getAd() {
        return ad;
    }

    public int getDayaniklilik() {
        return dayaniklilik;
    }

    public int getVurusGucu() {
        return vurusGucu;
    }
}



//Abstract SavasAraci Sınıfı
abstract class SavasAraci {
 protected int dayaniklilik;
 protected int vurusGucu;
 protected String sinifAdi;

 public SavasAraci(int dayaniklilik, int vurusGucu, String sinifAdi) {
     this.dayaniklilik = dayaniklilik;
     this.vurusGucu = vurusGucu;
     this.sinifAdi = sinifAdi;
 }

 public abstract int avantajVurusGucu(String rakipSinif);


 public String getImagePath() {
     return getClass().getResource("/resim/uçak.jpg").toExternalForm();
 }

}

    class Ucak extends SavasAraci {
    public Ucak() {
        super(20, 50, "Uçak");
    }

    @Override
    public int avantajVurusGucu(String rakipSinif) {
        return vurusGucu;
    }

    @Override
    public String getImagePath() {
        return getClass().getResource("/resim/uçak.jpg").toExternalForm(); // Uçak resmi
    }
}


//Siha Kartı
    class Siha extends SavasAraci {
        public Siha() {
            super(15, 60, "Siha");
        }

        @Override
        public int avantajVurusGucu(String rakipSinif) {
            return vurusGucu;
        }

        @Override
        public String getImagePath() {
            return getClass().getResource("/resim/siha.jpg").toExternalForm(); // Siha resmi
        }
    }


    class Obus extends SavasAraci {
        public Obus() {
            super(20, 40, "Obüs");
        }

        @Override
        public int avantajVurusGucu(String rakipSinif) {
            return vurusGucu;
        }

        @Override
        public String getImagePath() {
            return getClass().getResource("/resim/obus.jpg").toExternalForm(); // Obüs resmi
        }
    }


    class Sida extends SavasAraci {
        public Sida() {
            super(15, 65, "Sida");
        }

        @Override
        public int avantajVurusGucu(String rakipSinif) {
            return vurusGucu;
        }

        @Override
        public String getImagePath() {
            return getClass().getResource("/resim/sida.jpg").toExternalForm(); // Sida resmi
        }
    }


    class Firkateyn extends SavasAraci {
        public Firkateyn() {
            super(25, 45, "Firkateyn");
        }

        @Override
        public int avantajVurusGucu(String rakipSinif) {
            return vurusGucu;
        }

        @Override
        public String getImagePath() {
            return getClass().getResource("/resim/firkateyn.jpg").toExternalForm(); // Fırkateyn resmi
        }
    }


    class KFS extends SavasAraci {
        public KFS() {
            super(10, 70, "KFS");
        }

        @Override
        public int avantajVurusGucu(String rakipSinif) {
            return vurusGucu;
        }

        @Override
        public String getImagePath() {
            return getClass().getResource("/resim/kfs.jpg").toExternalForm(); // KFS resmi
        }
    }


// Oyuncu ve Bilgisayar Sınıfları 
class Oyuncu {
    private int oyuncuID;
    private String oyuncuAdi;
    private int skor;
    protected ArrayList<SavasAraci> kartListesi;
    private ArrayList<SavasAraci> kullanilanKartlar;

    public Oyuncu(int oyuncuID, String oyuncuAdi) {
        this.oyuncuID = oyuncuID;
        this.oyuncuAdi = oyuncuAdi;
        this.skor = 0;
        this.kartListesi = new ArrayList<>();
        this.kullanilanKartlar = new ArrayList<>();
    }

    public void kartEkle(SavasAraci kart) {
        kartListesi.add(kart);
    }

    public void skorEkle(int puan) {
        this.skor += puan;
    }

    public int getSkor() {
        return this.skor;
    }

    public SavasAraci kartSec(int index) {
        SavasAraci kart = kartListesi.remove(index);
        kullanilanKartlar.add(kart);
        return kart;
    }

    public void kartlariYenidenAktifEt() {
        kartListesi.addAll(kullanilanKartlar);
        kullanilanKartlar.clear();
    }

    public boolean kartKaldiMi() {
        return !kartListesi.isEmpty();
    }

    public void skorGoster() {
        System.out.println(oyuncuAdi + " Skoru: " + skor);
    }
}

class Bilgisayar extends Oyuncu {
    private Random random;

    public Bilgisayar(int oyuncuID) {
        super(oyuncuID, "Bilgisayar");
        this.random = new Random();
    }

    public SavasAraci kartSec() {
        if (!kartListesi.isEmpty()) {
            return kartListesi.remove(random.nextInt(kartListesi.size()));
        }
        return null;
    }


    // Rastgele Kart Oluşturma Metodu
    public static SavasAraci randomKartOlustur(Random random) {
        switch (random.nextInt(6)) {
            case 0:
                return new Ucak();
            case 1:
                return new Siha();
            case 2:
                return new Obus();
            case 3:
                return new KFS();
            case 4:
                return new Firkateyn();
            case 5:
                return new Sida();
            default:
                return new Ucak(); 
        }
    }
}
