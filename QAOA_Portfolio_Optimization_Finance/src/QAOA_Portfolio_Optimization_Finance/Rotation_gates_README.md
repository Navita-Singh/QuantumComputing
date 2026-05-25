# Quantum Rotation Gates: Rx, Ry, and Rz

In quantum computing, the rotation gates Rx, Ry, and Rz rotate a qubit state around the **x-axis**, **y-axis**, and **z-axis** of the Bloch sphere.

A general qubit is:

$$|\psi\rangle = \alpha|0\rangle + \beta|1\rangle$$

where:

$$|\alpha|^2 + |\beta|^2 = 1$$

The Bloch sphere helps visualize how these rotations change the quantum state.

---

## 1. Bloch Sphere Intuition

On the Bloch sphere:

- **North pole** = |0⟩
- **South pole** = |1⟩

The state vector points somewhere on the sphere surface.

- **Rx**: rotates around x-axis
- **Ry**: rotates around y-axis
- **Rz**: rotates around z-axis

---

## 2. The Rx Gate

The Rx(θ) gate rotates the qubit by angle θ around the x-axis.

Its matrix is:

$$R_x(\theta) = \begin{pmatrix} \cos(\theta/2) & -i\sin(\theta/2) \\ -i\sin(\theta/2) & \cos(\theta/2) \end{pmatrix}$$

---

## 3. Visualizing Rx on the Bloch Sphere

Imagine the qubit initially at |0⟩ (top of sphere).

Applying Rx:
- spins the state around the horizontal x-axis
- gradually moves probability from |0⟩ to |1⟩

**Examples:**

| Input | Output |
|-------|--------|
| Rx(0) | still \|0⟩ |
| Rx(π) | becomes \|1⟩ |
| Rx(π/2) | equal superposition |

**Conceptually:**

```
        |0>
         ●
        /|
       / |
------●--|------  x-axis rotation
       \ |
        \|
         ●
        |1>
```

The state vector rotates vertically around the x-axis.

---

## 4. How Rx Changes Amplitudes

Starting with:

$$|0\rangle = \begin{pmatrix} 1 \\ 0 \end{pmatrix}$$

Applying Rx(θ):

$$R_x(\theta)|0\rangle = \begin{pmatrix} \cos(\theta/2) \\ -i\sin(\theta/2) \end{pmatrix}$$

Now:
- Amplitude of |0⟩ becomes: cos(θ/2)
- Amplitude of |1⟩ becomes: −i·sin(θ/2)

Probabilities are:

$$P(0) = \cos^2(\theta/2)$$
$$P(1) = \sin^2(\theta/2)$$

So Rx literally redistributes probability amplitude between |0⟩ and |1⟩.

---

## 5. The Ry Gate

The Ry gate rotates around the y-axis.

**Matrix:**

$$R_y(\theta) = \begin{pmatrix} \cos(\theta/2) & -\sin(\theta/2) \\ \sin(\theta/2) & \cos(\theta/2) \end{pmatrix}$$

**Applied to |0⟩:**

$$R_y(\theta)|0\rangle = \begin{pmatrix} \cos(\theta/2) \\ \sin(\theta/2) \end{pmatrix}$$

Notice:
- Both amplitudes are **real numbers**
- No imaginary *i* term appears

Probabilities:

$$P(0) = \cos^2(\theta/2)$$
$$P(1) = \sin^2(\theta/2)$$

So Ry also changes amplitudes between |0⟩ and |1⟩.

---

## 6. Difference Between Rx and Ry

Both gates redistribute amplitudes, but differently:

| Gate | Rotation Axis | Complex Phase? | Changes Probability? |
|------|--------------|----------------|----------------------|
| Rx   | x-axis       | Yes (i)        | Yes                  |
| Ry   | y-axis       | Usually real amplitudes | Yes           |
| Rz   | z-axis       | Mostly phase only | Usually no        |

---

## 7. The Rz Gate

The Rz gate rotates around the z-axis.

**Matrix:**

$$R_z(\theta) = \begin{pmatrix} e^{-i\theta/2} & 0 \\ 0 & e^{i\theta/2} \end{pmatrix}$$

Unlike Rx and Ry:
- Rz mainly changes the **relative phase**
- It usually does **not** move probability between |0⟩ and |1⟩

So it changes *how amplitudes interfere*, not the measurement probabilities directly.

---

## 8. Physical Intuition

You can think of:

- **Rx and Ry:**
  - Tilting the state vector
  - Moving population between |0⟩ and |1⟩

- **Rz:**
  - Spinning the vector around itself
  - Changing phase relationships

These rotation gates are fundamental because:
- Every single-qubit quantum gate can be built from combinations of Rx, Ry, and Rz
- Variational quantum circuits heavily use them
- They are the "continuous control knobs" of quantum states
