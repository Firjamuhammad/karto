<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>GPS Location</title>
<link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
<style>
  #map {
    height: 400px;
    width: 100%;
    margin-bottom: 20px;
  }
  #coordinates {
    margin-top: 10px;
    font-size: 16px;
  }
</style>
</head>
<body>

<h2>GPS Location</h2>

<p>
    <strong>Nama Mata Kuliah:</strong> Kartografi dan Geospasial<br>
    <strong>Program Studi:</strong> Sistem Informasi Kota Cerdas<br>
    <strong>Kampus:</strong> Politeknik Negeri Banjarmasin<br>
    <strong>Dosen Pengampu:</strong> Agus SBN<br>
    <strong>Nama:</strong> Muhammad Noor Firjatullah<br>
    <strong>NIM:</strong> C050422013<br>
    <strong>Kelas:</strong> SIKC 4A<br>
    <strong>Tanggal:</strong> 22 April 2024<br>
    <strong>Alamat:</strong> <span id="address"></span>
</p>

<div id="map"></div>
<div id="coordinates">Latitude: <span id="lat"></span>, Longitude: <span id="lng"></span></div>

<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
<script>
function initMap() {
  var map = L.map('map').setView([0, 0], 13); 

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors',
    maxZoom: 18,
  }).addTo(map);

  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(function(position) {
      var lat = position.coords.latitude;
      var lng = position.coords.longitude;

      map.setView([lat, lng], 13);

      L.marker([lat, lng]).addTo(map)
        .bindPopup('Posisi Anda<br>Latitude: ' + lat + '<br>Longitude: ' + lng).openPopup();

      document.getElementById('lat').textContent = lat.toFixed(6);
      document.getElementById('lng').textContent = lng.toFixed(6);

      // Memanggil fungsi untuk mendapatkan alamat
      getAddress(lat, lng);
    }, function() {
      alert("Gagal mendapatkan posisi Anda.");
    });
  } else {
    alert("Geolocation tidak didukung oleh browser ini.");
  }
}

initMap();

function getLocation() {
    if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(showPosition, showError);
    } else {
        document.getElementById("location").innerHTML = "Geolocation tidak didukung oleh browser Anda.";
    }
}

function showPosition(position) {
    var latitude = position.coords.latitude;
    var longitude = position.coords.longitude;

    document.getElementById("location").innerHTML = "Lokasi Anda:<br>Latitude: " + latitude + "<br>Longitude: " + longitude;

    var mapUrl = "https://www.mapquestapi.com/staticmap/v5/map?key=YOUR_API_KEY&center=" + latitude + "," + longitude + "&size=400,300&zoom=15&locations=" + latitude + "," + longitude;
    document.getElementById("map").innerHTML = "<img src='" + mapUrl + "'>";

    // Memanggil fungsi untuk mendapatkan alamat
    getAddress(latitude, longitude);
}

function getAddress(latitude, longitude) {
    fetch('https://nominatim.openstreetmap.org/reverse?format=jsonv2&lat=' + latitude + '&lon=' + longitude)
        .then(response => response.json())
        .then(data => {
            var address = data.display_name;
            document.getElementById('address').textContent = address;
        })
        .catch(error => {
            console.error('Error fetching address:', error);
            document.getElementById('address').textContent = "Alamat tidak dapat ditemukan.";
        });
}

</script>

<button onclick="getLocation()">Dapatkan Lokasi Saya</button>

</body>
</html>
