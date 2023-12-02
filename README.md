# Lab8Web
# Lab8Web 
## Profil
| Variable | Isi |
| -------- | --- |
| **Nama** | Ricky alfian saputra |
| **NIM** | 312210279 |
| **Kelas** | TI.22.A4 |
| **Mata Kuliah** | Pemrograman WEB |

### Instruksi Praktikum
1. Buatlah repository baru dengan nama Lab8Web.
2. Kerjakan semua latihan yang diberikan sesuai urutannya.
3. Screenshot setiap perubahannya.
4. Buatlah file README.md dan tuliskan penjelasan dari setiap langkah praktikum 
beserta screenshotnya.
5. Commit hasilnya pada repository masing-masing.
6. Kirim URL repository pada e-learning ecampus

# Langkah-langkah Praktikum
## Membuat Database & Tabel
```SQL
CREATE DATABASE latihan1;
&
CREATE TABLE data_barang (
 id_barang int(10) auto_increment Primary Key,
 kategori varchar(30),
 nama varchar(30),
 gambar varchar(100),
 harga_beli decimal(10,0),
 harga_jual decimal(10,0),
 stok int(4)
);
```
**Hasil :**

![](https://github.com/ricky1211/Lab8Web/blob/main/LAB8/Screenshot%20(335).png?raw=true)

#### *Note :*

## Menambahkan data
```SQL
INSERT INTO data_barang (kategori, nama, gambar, harga_beli, harga_jual, stok)
VALUES ('Elektronik', 'HP Samsung Android', 'hp_samsung.jpg', 2000000, 2400000, 5),
('Elektronik', 'HP Xiaomi Android', 'hp_xiaomi.jpg', 1000000, 1400000, 5),
('Elektronik', 'HP OPPO Android', 'hp_oppo.jpg', 1800000, 2300000, 5);
```
**Hasil :**

![](https://github.com/ricky1211/Lab8Web/blob/main/LAB8/Screenshot%20(339).png?raw=true)

#### *Note :*

## Membuat File koneksi database
```PHP
<?php
$host = "localhost";
$user = "root";
$pass = "";
$db = "latihan1";

$conn = mysqli_connect($host, $user, $pass, $db);

if (!$conn) {
    die("Koneksi ke server gagal: " . mysqli_connect_error());
} else {
    echo "Koneksi berhasil";
}

```
**Hasil :**

![](https://github.com/ricky1211/Lab8Web/blob/main/LAB8/Screenshot%20(334).png?raw=true)

#### *Note :*
Buat file baru dengan nama `koneksi.php`, Buka melalui browser untuk menguji koneksi database (untuk menyampilkan pesan 
koneksi berhasil, uncomment pada perintah echo “koneksi berhasil”; 


## Membuat file index untuk menampilkan data (Read)
```PHP
<?php
include("koneksi.php");


if (isset($_GET['delete'])) {
    $idToDelete = $_GET['delete'];


    $deleteSql = "DELETE FROM data_barang WHERE id_barang = $idToDelete";
    $deleteResult = mysqli_query($conn, $deleteSql);


    if ($deleteResult) {
        header("Location: index.php");
        exit();
    } else {
        echo "Error deleting record: " . mysqli_error($conn);
    }
}

// query untuk menampilkan data
$sql = 'SELECT * FROM data_barang';
$result = mysqli_query($conn, $sql);
?>

<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <link href="style.css" rel="stylesheet" type="text/css" />
    <title>Data Barang</title>
</head>

<body>
    <div class="container">
        <h1>Data Barang</h1>
        <div class="main">
            <table>
                <tr>
                    <th>Gambar</th>
                    <th>Nama Barang</th>
                    <th>Kategori</th>
                    <th>Harga Jual</th>
                    <th>Harga Beli</th>
                    <th>Stok</th>
                    <th>Aksi</th>
                </tr>
                <?php if ($result) : ?>
                    <?php while ($row = mysqli_fetch_array($result)) : ?>
                        <tr>
                            <td><img src="gambar/<?= $row['gambar']; ?>" alt="<?= $row['nama']; ?>"></td>
                            <td><?= $row['nama']; ?></td>
                            <td><?= $row['kategori']; ?></td>
                            <td><?= $row['harga_beli']; ?></td>
                            <td><?= $row['harga_jual']; ?></td>
                            <td><?= $row['stok']; ?></td>
                            <td>
                                <a class="delete" href="?delete=<?= $row['id_barang']; ?>" onclick="return confirm('Are you sure you want to delete this item?')">Hapus</a>
                                <a class="ubah" href="ubah.php?id=<?= $row['id_barang']; ?>">Ubah</a>
                            </td>
                        </tr>
                    <?php endwhile;
                else : ?>
                    <tr>
                        <td colspan="7">Belum ada data</td>
                    </tr>
                <?php endif; ?>
            </table>
            <table>
                <th><a class="tambah" href="tambah_barang.php">Tambahkan Barang</a></th>
            </table>
        </div>
    </div>
</body>

</html>
```
**Hasil :**

![](https://github.com/ricky1211/Lab8Web/blob/main/LAB8/Screenshot%20(336).png?raw=true)

#### *Note :*
Buat file baru dengan nama `index.php`.
Disini saya sudah menambahkan beberapa data sebagai bukti bahawa program pada file `tambah_barang.php` berhasil.
Dan pada fitur deleted, berada dalam file yang sama.

## Menambah Data (Create)
```PHP
<?php
include("koneksi.php");

if ($_SERVER["REQUEST_METHOD"] == "POST") {

    $nama = $_POST['nama'];
    $kategori = $_POST['kategori'];
    $harga_jual = $_POST['harga_jual'];
    $harga_beli = $_POST['harga_beli'];
    $stok = $_POST['stok'];

    // Upload image file
    $gambar = $_FILES['gambar']['name'];
    $gambar_tmp = $_FILES['gambar']['tmp_name'];
    $gambar_path = "gambar/" . $gambar;
    move_uploaded_file($gambar_tmp, $gambar_path);


    $insertSql = "INSERT INTO data_barang (nama, kategori, harga_jual, harga_beli, stok, gambar) 
                  VALUES ('$nama', '$kategori', '$harga_jual', '$harga_beli', '$stok', '$gambar')";

    $insertResult = mysqli_query($conn, $insertSql);


    if ($insertResult) {
        header("Location: index.php");
        exit();
    } else {
        echo "Error adding record: " . mysqli_error($conn);
    }
}
?>

<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <link href="style.css" rel="stylesheet" type="text/css" />
    <title>Tambah Barang</title>
    <style>
        .main {
            margin: auto;
            width: 35%;
            height: 35%;
        }

        table {
            border: 1px solid;
            padding: 2px;
            margin: 2px;
            border-radius: 3px;
        }

        label {
            display: block;
            margin-bottom: 8px;
        }

        input {
            width: 100%;
            padding: 8px;
            margin-bottom: 16px;
            box-sizing: border-box;
        }

        button {
            background-color: #000dff;
            color: white;
            padding: 10px 15px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }

        button:hover {
            background-color: #367efa;
        }
    </style>

</head>

<body>
    <div class="container">
        <h1>Tambah Barang</h1>
        <div class="main">
            <form action="" method="post" enctype="multipart/form-data">
                <table>
                    <tr>
                        <td><label for="nama">Nama Barang:</label></td>
                        <td><input type="text" name="nama" required></td>
                    </tr>
                    <tr>
                        <td><label for="kategori">Kategori:</label></td>
                        <td><select name="kategori">
                                <option value="Komputer">Komputer</option>
                                <option value="Elektronik">Elektronik</option>
                                <option value="Hand Phone">Hand Phone</option>
                            </select></td>
                    </tr>
                    <tr>
                        <td><label for="harga_jual">Harga Jual:</label></td>
                        <td><input type="number" name="harga_jual" required></td>
                    </tr>
                    <tr>
                        <td><label for="harga_beli">Harga Beli:</label></td>
                        <td><input type="number" name="harga_beli" required></td>
                    </tr>
                    <tr>
                        <td><label for="stok">Stok:</label></td>
                        <td><input type="number" name="stok" required></td>
                    </tr>
                </table>
                <tr>
                    <td>
                        <p>Gambar : <input type="file" name="gambar" accept="image/*" required></p>
                    </td>
                </tr>
                <tr>
                    <td colspan="2"><button type="submit">tambah barang</button></td>
                </tr>
        </div>
    </div>
</body>

</html>

</html>
```
**Hasil :**

![](https://github.com/ricky1211/Lab8Web/blob/main/LAB8/Screenshot%20(338).png?raw=true)

#### *Note :*
Buat file baru dengan nama `tambah.php`.

## Mengubah Data (Update)
```PHP
<?php
include("koneksi.php");

if ($_SERVER["REQUEST_METHOD"] == "POST") {

    $idToUpdate = $_POST['id_barang'];
    $newData = array(

        'nama' => $_POST['nama'],
        'kategori' => $_POST['kategori'],
        'harga_beli' => $_POST['harga_beli'],
        'harga_jual' => $_POST['harga_jual'],
        'stok' => $_POST['stok']

    );

    if ($_FILES['gambar']['size'] > 0) {

        $targetDirectory = "gambar/";
        $targetFileName = $targetDirectory . basename($_FILES['gambar']['name']);
        move_uploaded_file($_FILES['gambar']['tmp_name'], $targetFileName);



        $newGambar = $_FILES['gambar']['name'];
        $updateImageSql = "UPDATE data_barang SET gambar = '$newGambar' WHERE id_barang = $idToUpdate";
        mysqli_query($conn, $updateImageSql);
    }

    $updateSql = "UPDATE data_barang SET ";
    foreach ($newData as $field => $value) {
        $updateSql .= "$field = '$value', ";
    }
    $updateSql = rtrim($updateSql, ', ') . " WHERE id_barang = $idToUpdate";

    $updateResult = mysqli_query($conn, $updateSql);


    if ($updateResult) {
        header("Location: index.php");
        exit();
    } else {
        echo "Error updating record: " . mysqli_error($conn);
    }
}


if (isset($_GET['id'])) {
    $idToEdit = $_GET['id'];


    $editSql = "SELECT * FROM data_barang WHERE id_barang = $idToEdit";
    $editResult = mysqli_query($conn, $editSql);

    if ($editResult && $row = mysqli_fetch_array($editResult)) {

?>
        <!DOCTYPE html>
        <html lang="en">

        <head>
            <meta charset="UTF-8">
            <link href="style.css" rel="stylesheet" type="text/css" />
            <title>Edit Barang</title>

            <style>
                .main {
                    margin: auto;
                    width: 50%;
                    height: 50%;
                }

                button {
                    background-color: #000dff;
                    color: white;
                    padding: 10px 15px;
                    border: none;
                    border-radius: 4px;
                    cursor: pointer;
                    margin-top: 5px;
                }

                button:hover {
                    background-color: #367efa;
                }
            </style>
        </head>

        <body>
            <div class="container">
                <h1>Edit Barang</h1>
                <div class="main">
                    <form method="post" action="">
                        <table>
                            <tr>
                                <td><label>Nama Barang:</label></td>
                                <td><input type="text" name="nama" value="<?= $row['nama']; ?>" required></td>
                            </tr>
                            <tr>
                                <td><label>Kategori:</label></td>
                                <td>
                                    <select name="kategori" required>
                                        <option value="Komputer" <?= ($row['kategori'] == 'Komputer') ? 'selected' : ''; ?>>Komputer</option>
                                        <option value="Elektronik" <?= ($row['kategori'] == 'Elektronik') ? 'selected' : ''; ?>>Elektronik</option>
                                        <option value="Hand phone" <?= ($row['kategori'] == 'Hand phone') ? 'selected' : ''; ?>>Hand phone</option>
                                    </select>
                                </td>
                            </tr>

                            <tr>
                                <td><label>Harga Beli:</label></td>
                                <td><input type="text" name="harga_beli" value="<?= $row['harga_beli']; ?>" required></td>
                            </tr>
                            <tr>
                                <td><label>Harga Jual:</label></td>
                                <td><input type="text" name="harga_jual" value="<?= $row['harga_jual']; ?>" required></td>
                            </tr>
                            <tr>
                                <td><label>Stok:</label></td>
                                <td><input type="text" name="stok" value="<?= $row['stok']; ?>" required></td>
                            </tr>
                            <tr>
                                <td><label for="gambar">Gambar:</label></td>
                                <td><input type="file" name="gambar"></td>
                            </tr>
                        </table>
                        <table class="button">
                            <tr>
                                <input type="hidden" name="id_barang" value="<?= $row['id_barang']; ?>">
                                <button type="submit">Simpan</button>
                            </tr>
                        </table>
                    </form>
                </div>
            </div>
        </body>

        </html>
<?php
    } else {
        echo "Barang not found";
    }
} else {
    echo "ID parameter is missing";
}
?>
```
**Hasil :**

![](https://github.com/ricky1211/Lab8Web/blob/main/LAB8/Screenshot%20(337).png?raw=true)

#### *Note :*
Buat file baru dengan nama` ubah.php`,

**[---KEMBALI-->](#Profil)**
