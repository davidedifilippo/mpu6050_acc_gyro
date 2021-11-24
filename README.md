

## Utilizzo della libreria MPU6050

- **giroscopio**  misura la velocità di rotazione attorno agli assi in gradi al secondo
- **accelerometro**  misura l'accelerazione nelle tre direzioni x, y, z 

## Utilizzo della libreria

Si include la libreria di gestione del sensore:

    #include "MPU6050.h"

Da questo momento è disponibile la classe di dispositivi MPU6050. Si crea quindi un oggetto del tipo MPU6050.

    MPU6050 mpu6050;

Si inizializza il sensore dichiarando i fondo scala per il giroscopio e l'accelerometro:

    mpu.begin(MPU6050_SCALE_2000DPS, MPU6050_RANGE_2G);

**Se non si specifica nessun valore nella chiamata allora i valori di default risulteranno +-2G per l'accelerazione e +-250°/s per il giroscopio**

Per controllare il fondo scala impostato per la'ccelerometro si può utilizzare la seguente porzione di codice:
        
        Serial.print(" Fondo scala accelerometro:         ");
        
        switch(mpu.getRange())
        {
        case MPU6050_RANGE_16G:            Serial.println("+/- 16 g"); break;
        case MPU6050_RANGE_8G:             Serial.println("+/- 8 g"); break;
        case MPU6050_RANGE_4G:             Serial.println("+/- 4 g"); break;
        case MPU6050_RANGE_2G:             Serial.println("+/- 2 g"); break;
        }  
        
        Serial.print(" * Gyroscope:         ");
        switch(mpu.getScale())
        {
        case MPU6050_SCALE_2000DPS:        Serial.println("2000 dps"); break;
        case MPU6050_SCALE_1000DPS:        Serial.println("1000 dps"); break;
        case MPU6050_SCALE_500DPS:         Serial.println("500 dps"); break;
        case MPU6050_SCALE_250DPS:         Serial.println("250 dps"); break;
        } 

### Lettura dei **dati grezzi** dell'accelerometro

    Vector rawAcc = mpu.readRawAccel(); 

serve per leggere gli otto bit alti e bassi dei dati a 16 bit che rappresentano in complemento a due i valori dell'accelerazione nelle tre direzioni di riferimento x y z. 

Se il fondo scala fosse +-2g allora in corrispondenza di una accelerazione pari a 2g avrei il massimo positivo ossia 0111 1111 1111 1111 o anche 0x7FF.

### Lettura dei **dati normalizzati** dell'accelerometro

La **normalizzazione** serve per riportare l'accelerazione in metri al secondo quadro a partire dal dato digitale:

    Vector Acc = mpu.readNormalizeAccel();

se divido per 9.81 questa quantità, troverò l'accelerazione in g. Ad esempio se il sensore è in piano ax = 0g, ay = 0g, az = -1 che risulta leggibile e comprensibile. Per accedere al dato relativo al singolo asse abbiamo:

        Serial.print(" Xraw = ");
        Serial.print(rawAccel.XAxis);

## Lettura dei dati dal giroscopio

Anche in questo caso possiamo leggere i dati grezzi oppure i dati normalizzati. Se il fondo scala fosse +-250°/s allora in corrispondenza della velocità di rotazione massima avrei il massimo positivo ossia 0111 1111 1111 1111 o anche 0x7FF.


        Vector rawGyro = mpu.readRawGyro();
        
Normalizzando avrei la velocità di rotazione intorno ai tre assi di riferimento in gradi al secondo.

        Vector normGyro = mpu.readNormalizeGyro();
        
 Per acccedere ai dati del singolo asse:
 
        Serial.print(" Xraw = ");
        Serial.print(rawGyro.XAxis);
        
        
## Calcolo della posizione angolare 

Dato che conosciamo la velocità angolare del sensore possiamo risalire alla posizione angolare **rispetto alla posizione iniziale al reset** moltiplicando tale velocità per il tempo. In questo modo si ottiene lo spazio angolare percorso (in realtà per alte velocità il risultato nel tempo non è accurato)

        unsigned long timer = 0;
        float timeStep = 0.01; 
        
 Il calcolo viene eseguto ogni 10 milisecondi.

        float yaw = 0;
        
 In questo caso interessa la rotazione attorno all'asse z (imbardata)

        void loop()
        {
        timer = millis();
        Vector norm = mpu.readNormalizeGyro();
        yaw = yaw + norm.ZAxis * timeStep;
        ......


Aspetto che passino i millisecondi restanti dei dieci disponibili
  
        delay((timeStep*1000) - (millis() - timer));
 
        
## Calibrazione del sensore

Se si vuole calibrare il giroscopio basta richiamare la funzione:

        mpu.calibrateGyro(); 
        
 ## Impostazione sensibilità       
     
 Se si vuole impostare la sensibilità massima basta impostarla a livello 3 (dove 0 è il livello minimo)
 
        mpu.setThreshold(3);
 
