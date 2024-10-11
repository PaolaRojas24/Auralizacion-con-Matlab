# Auralizacion-con-Matlab
Alumna: Paola Rojas Domínguez A01737136 </br>
## Descripción
Desarrollo de una interfaz gráfica (GUI) en MATLAB utilizando AppDesigner que permite cargar tres archivos WAV para aplicar un algoritmo de auralización. La GUI incluye un script que implementa dicho algoritmo utilizando cualquiera de los archivos de audio cargados. Además, incorpora controles que permitan seleccionar un archivo WAV específico y aplicarle el efecto de auralización indicado por el profesor. La GUI también generará un archivo WAV de salida que refleje el efecto aplicado, el cual podrá ser reproducido directamente desde la interfaz mediante algún control de reproducción.
## Script
La parte más importante del script es la función llamada `Auralizar`, esta sirve para procesar un archivo de audio seleccionado y aplicar una técnica de auralización utilizando una respuesta al impulso. </br> Primero, se actualiza la interfaz gráfica con un mensaje indicando que el proceso de auralización está en curso. 
````matlab
% Función para auralizar el archivo seleccionado con una sola respuesta al impulso
        function Auralizar(app, event)
            % Mostrar el mensaje "Auralizando..." en el Label process
            app.process.Text = 'Auralizando...';
            drawnow;  % Actualizar la interfaz gráfica antes de iniciar el proceso
````
Luego, se carga el archivo de audio seleccionado y se convierte a mono si es estéreo, lo mismo ocurre con la respuesta al impulso. 
````matlab
            selectedData = app.getSelectedFileData();  % Obtener el archivo seleccionado
            if isempty(selectedData)
                uialert(app.UIFigure, 'No se ha seleccionado ningún archivo para auralizar.', 'Error');
                return;
            end
        
            app.loadImpulseFile();  % Cargar impulso.wav si no ha sido cargado
        
            % Convertir el archivo de audio a mono si es estéreo
            if size(selectedData.data, 2) > 1
                audioIn = mean(selectedData.data, 2);  % Promedio de los canales para convertir a mono
            else
                audioIn = selectedData.data;  % Ya es mono
            end
        
            % Convertir la respuesta de impulso a mono si es necesario
            if size(app.ImpulseData.data, 2) > 1
                impulseResponse = mean(app.ImpulseData.data, 2);  % Promedio de los canales
            else
                impulseResponse = app.ImpulseData.data;  % Ya es mono
            end
````
Si las frecuencias de muestreo del audio y de la respuesta al impulso no coinciden, se ajustan mediante resampling. 
````matlab
            % Ajustar las frecuencias de muestreo si no coinciden
            fs = selectedData.Fs;  % Frecuencia de muestreo del audio original
            fs_ir = app.ImpulseData.Fs;  % Frecuencia de muestreo del impulso
        
            % Si las frecuencias no coinciden, hacer resample
            if fs_ir ~= fs
                impulseResponse = resample(impulseResponse, fs, fs_ir);
            end
````
Después, se realiza la convolución entre el audio y la respuesta al impulso, se normaliza la señal resultante, y se guarda la señal auralizada y la frecuencia de muestreo en la aplicación. Finalmente, se actualiza la interfaz gráfica para indicar que el proceso ha terminado.
```matlab
            % Realizar la convolución del audio con la respuesta de impulso
            audioOut = conv(audioIn, impulseResponse);
        
            % Normalizar el audio convolucionado
            audioOut = audioOut / max(abs(audioOut));
        
            % Almacenar la señal auralizada en la aplicación
            app.AuralizedSignal = audioOut;
        
            % Almacenar la frecuencia de muestreo para el guardado
            app.SamplingRate = fs;

            % Mostrar el mensaje "Proceso de auralización terminado" en el Label process
            app.process.Text = 'Proceso de auralización terminado';

        end
```
## Aplicación
La interfaz cuenta con tres botones para cargar tres archivos de audio, sus respectivos campos de texto para mostrar el nombre del archivo y un botón de reproducir para cada archivo. Un menú desplegable para escoger que archivo se va a auralizar, el botón que inicia dicho proceso, un botón para reproducir el archivo ya auralizado y un campo de texto con su respectivo botón para guardar el archivo con el audio resultante. </br>
</br>
![image](https://github.com/user-attachments/assets/10def74f-9eea-41e4-a851-efd067bac69c)
</br> 
</br> Video de demostración: https://youtu.be/xWNOuhFf5_M </br>
