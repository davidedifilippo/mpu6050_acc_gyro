

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

Se non si specifica nessun valore nella chiamata allora i valori di default risulteranno +-2G per l'accelerazione e +-250°/s per il giroscopio

Lettura dei **dati grezzi** dell'accelerometro:

    mpu.readRawAccel(); 

serve per leggere gli otto bit alti e bassi del dato a 16 bit che rappresenta in complemento a due il valore dell'accelerazione. Se il fondo scala 
fosse +-2g allora in corrispondenza di una accelerazione pari a 2g avrei il massimo positivo ossia 0111 1111 1111 1111 o anche 0x7FF.

La **normalizzazione** serve per riportare l'accelerazione in metri al secondo quadro a partire dal dato digitale:

    acc_normalizzata_ms = mpu.readNormalizeAccel()

se divido per 9.81 questan quantità trovo l'accelerazione in g. Ad esempio se il sensore è in piano ax = 0g, ay = 0g, az = -1 

##Lettura dei dati dal giroscopio


