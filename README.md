# Radar_Target_Generation_and_Detection
Radar target generation and detection project for udacity

## 2D CFAR Implementation

1- Determing the number of Training cells for each dimension. Similarly, Determing the number of guard cells.

```
Tr = 20;        %Number of training cells for range
Td = 18;        %Number of training cells for doppler   

Gr = 7;         %Number of guard cells for range
Gd = 7;         %Number of guard cells for doppler

```

2- Determining the threshold offset by SNR value in db

` offset = 12; `

3- Sliding the cell under test across the complete matrix.
  - Defining a `noise_level` vector to store the average noise across the training cells.
  - Calculating the `threshold`.
  - Checking if the cell under test `cut` is below or above threshold and modifying the matrix upon the condition.

```
for i = Tr + Gr + 1:Nr/2 - (Tr + Gr)
    for j = Td + Gd + 1:Nd - (Gd + Td)
        
        noise_level = zeros(1,1);
        
        for p = i - (Tr + Gr):i + (Gr + Tr)
            for q = j - (Td + Gd):j + (Gd + Td)
            
                if (abs(i - p) > Gr || abs(j - q) > Gd)
                    noise_level = noise_level + db2pow(RDM(p, q));
                end
            end
        end
        
        
        threshold = pow2db(noise_level/((2 * (Td + Gd + 1)  * 2 * (Tr + Gr+ 1) - (Gr * Gd) - 1)));
        threshold = threshold + offset;

        cut = RDM(i,j);
        
        if(cut > threshold)
            RDM(i,j) = 1;
        else
            RDM(i,j) = 0;
        end
        
        
        
    end
end

```
  
4- The cell under test cannot be located at the edges of matrix, as the training cells are occupying them, so, set those values to `0`

```
for i = 1 : Nr/2
    for j = 1:Nd
        
        if (RDM(i,j) ~= 0 && RDM(i,j) ~= 1 )
             RDM(i,j) = 0;
        end
    end
end

```

# Simulation Results
