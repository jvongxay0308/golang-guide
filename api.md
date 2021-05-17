API Design Guide

# ສາລະບານ (Table of Content)

- [ສາລະບານ (Table of Content)](#ສາລະບານ-table-of-content)
- [Introduction](#introduction)
- [Resource Oriented Design](#resource-oriented-design)
  - [What is a REST API?](#what-is-a-rest-api)
  - [Design Flow](#design-flow)
  - [Resources](#resources)
  - [Methods](#methods)
- [Resource Names](#resource-names)
- [Standard Methods](#standard-methods)
- [Custom Methods](#custom-methods)
- [Standard Fields](#standard-fields)
- [Errors](#errors)
- [Naming Conventions](#naming-conventions)
- [Design Patterns](#design-patterns)
- [Documentation](#documentation)
- [Using proto3](#using-proto3)
- [Versioning](#versioning)
- [Compatibility](#compatibility)
- [Directory Structure](#directory-structure)
- [File Structure](#file-structure)
- [Glossary](#glossary)
- [Changelog](#changelog)

# Introduction

# Resource Oriented Design

ຈຸດປະສົງຂອງເອກະສານນີ້ແມ່ນເພື່ອຊ່ວຍໃຫ້ນັກພັດທະນາສາມາດອອກແບບ network API **ທີ່ລຽບງ່າຍ (simple), ສອດຄ້ອງ (consistent) ແລະງ່າຍໃນການໃຊ້ງານ (easy-to-use)**. ໃນຂະນະດຽວກັນຍັງຊ່ວຍລວມການອອກແບບດ້ວຍ socket-based RPC APIs ແລະ HTTP-based REST APIs ເຂົ້າໄວ້ນຳກັນອີກດ້ວຍ.

RPC APIs ມັກຖືກອອກແບບໃນແງ່ຂອງ interface ແລະ method (ວິທີການ). ເພາະມີການເພີ່ມຂຶ້ນເລື້ອຍໆຂອງ interface ແລະວິທີການ ສຸດທ້າຍຈະກາຍເປັນ API ທີ່ສັບສົນ ເນື່ອງຈາກນັກພັດທະນາຕ້ອງຮຽນຮູ້ແຕ່ລະວິທີການເທື່ອລະຢ່າງໄປ ເຫັນໄດ້ວ່າສິ່ງເຫຼົ່ານີ້ໃຊ້ເວລາດົນ ແລະເກິດຂໍ້ຜິດພາດໄດ້ງ່າຍ.

ສະຖາປັດຕະຍາກຳ [REST] ຖືກອອກແບບມາເພື່ອເຮັດວຍຽກຮ່ວມກັບ `HTTP/1.1` ເປັນຫຼັກ ແຕ່ຍັງຊ່ວຍແກ້ໄຂບັນຫານີ້ດ້ວຍ. ຫຼັກການສຳຄັນຄືການກຳນົດຊື່ຊັບພະຍາກອນ (named resources) ຊຶ່ງສາມາດຈັດການໄດ້ດ້ວຍວິທີການຈຳນວນນ້ອຍ. ຊັບພະຍາກອນ ແລະວິທີການເອີ້ນວ່າ **ຄຳນາມ (nouns)** ແລະ **ຄຳກິລິຍາ (verbs)** ຂອງ API. ດ້ວຍຂໍ້ກຳນົດຂອງ HTTP ຊື່ຊັບພະຍາກອນຈະຈັບຄູ່ກັບ URL ແລະວິທີການຈະຈັບຄູ່ກັບວິທີການຂອງ HTTP `POST`, `GET`, `PUT`, `PATCH` ແລະ `DELETE` ສົ່ງຜົນເຮັດໃຫ້ສິ່ງທີ່ຕ້ອງຮຽນຮູ້ນ້ອຍລົງຫຼາຍ ເນື່ອງຈາກນັກພັດທະນາສາມາດໃຫ້ຄວາມສຳຄັນກັບຊັບພະຍາກອນ ແລະຄວາມສຳພັນຂອງມັນ.

[rest]: https://en.wikipedia.org/wiki/Representational_state_transfer

ໃນອິນເຕີເນັດ HTTP REST API ເປັນປະສົບຜົນສຳເລັດຢ່າງງົດງາມໃນປິ 2010 ປະມານ 74% ຂອງ network API ສາທາລະນຳເປັນ HTTP REST (ຫຼືຄ້າຍຄື REST) API ຊຶ່ງສ່ວນໃຫຍ່ໃຊ້ JSON ເປັນ format.

ເຖິງວ່າ HTTP/JSON APIs ຈະໄດ້ຮັບຄວາມນິຍົມຢ່າງຫຼວງຫຼາຍໃນອິນເຕີເນັດ ແຕ່ປະລິມານການຮັບສົ່ງຂໍ້ມູນຈະນ້ອຍກວ່າ RPC API ຕົວຢ່າງເຊັ່ນ ປະມານເຄິ່ງໜຶ່ງຂອງປະລິມານການໃຊ້ອິນເຕີເນັດໃນອາເມຣິກາແມ່ນເນື້ອຫາວິດີໂອ ແລະມີພຽງບໍ່ເທົ່າໃດຄົນທີ່ຄິດຈະໃຊ້ HTTP/JSON APIs ໃຊ້ການສະແດງເນື້ອຫາດັ່ງກ່າວດ້ວຍເຫດຜົນດ້ານປະສິດທິພາບ. ຢູ່ພາຍໃນສູນຂໍ້ມູນ (Data centers) ຫຼາຍໆບໍລິສັດໃຊ້ socket-based RPC API ເພື່ອຮອງຮັບການຮັບສົ່ງຂໍ້ມູນເທິງເນັດເວີກ.

ໃນຄວາມເປັນຈິງຕ້ອງໃຊ້ທັງ RPC API ແລະ HTTP/JSON API ດ້ວຍເຫດຜົນຫຼາຍຢ່າງ ແລະຕາມຫຼັກການແລ້ວແພັດຟອມ API ຄວນໃຫ້ການສະໜັບສະໜູນທີ່ດີທີ່ສຸດສຳລັບ API ທຸກປະເພດ. ຄູ່ມືນີ້ຊ່ວຍໃຫ້ເຈົ້າອອກແບບ ແລະສ້າງ API ທີ່ສອດຄ້ອງກັບຫຼັກການນີ້ ໂດຍໃຊ້ຫຼັກການອອກແບບທີ່ເນັ້ນຊັບພະຍາກອນ (resource-oriented) ແລະກຳນົດຮູບແບບການອອກແບບທົ່ວໄປເພື່ອປັບປຸງການໃຊ້ງານ ແລະຫຼຸດຄວາມຊັບຊ້ອນ.

## What is a REST API?

REST API ຖືກສ້າງແບບຈຳລອງ (modeled) _ເປັນກຸ່ມ (collection)_ ຂອງ _ຊັບພະຍາກອນ_ ທີ່ສາມາດລະບຸທີ່ຢູ່ໄດ້ (ຄຳນາມຂອງ API). ຊັັບພະຍາກອນຖືກອ້າງອີງດ້ວຍຊື່ ແລະຈັດການຜ່ານວິທີການຈຳນວນໜຶ່ງ (ຫຼືືທີ່ເອີ້ນວ່າ verbs ຫຼື operations).

ວິທີມາດຕະຖານສຳຫຼັບ REST Google APIs (ຫຼືທີ່ເອີ້ນວ່າ REST methods) ໄດ້ແກ່ `List`, `Get`, `Create`, `Update` ແລະ `Delete`. _ວິທິການແບບກຳນົດເອງ (custom method)_ ຍັງມີໃຫ້ໃຊ້ສຳລັບນັກອອກແບບ API ສຳລັບຟັງຊັ່ນການເຮັດວຽກທີ່ບໍ່ໄດ້ຈັບຄູ່ກັບວິທີການມາດຕະຖານຢ່າງໃດໜຶ່ງ ເຊັ່ນ database transactions.

## Design Flow

ຄູ່ມືການອອກແບບແນະນຳໃຫ້ເຮັດຕາມຂັ້ນຕອນຕໍ່ໄປນີ້ເມື່ອອອກແບບ API ທີ່ເນັ້ນຊັບພະຍາກອນ (ລາຍລະອຽດເພີ່ມເຕີມຢູ່ຫົວຂໍ້ດ້ານລຸ່ມ):

- ກຳນົດປະເພດຂອງຊັບພະຍາກອນທີ່ API ສະໜອງໃຫ້.
- ກຳນົດຄວາມສຳພັບລະຫວ່າງຊັບພະຍາກອນ.
- ກຳນົດໂຄງຮ່າງຊື່ຊັບພະຍາກອນຕາມປະເພດ ແລະຄວາມສຳພັນ.
- ກຳນົດໂຄງຮ່າງຊັບພະຍາກອນ (schema).
- ກຳນົດວິທີການພື້ນຖານຂັ້ນຕ່ຳໃຫ້ກັບຊັບພະຍາກອນ.

## Resources

ໂດຍທົ່ວໄປ resource-oriented API ຈະຖືກຈຳລອງເປັນລຳດັບຊັ້ນ ໂດຍທີ່ແຕ່ລະໂໜດເປັນ _ຊັບພະຍາກອນຢ່າງງ່າຍ (simple resource)_ ຫຼື _ກຸ່ມຊັບພະຍາກອນ (collection resource)_ ເພື່ອຄວາມສະດວກໃຫ້ເອີ້ນວ່າ ຊັບພະຍາກອນ (resource) ຫຼື ກຸ່ມ (collection) ຕາມລຳດັບ.

- ຄໍເຣັກຊັ່ນປະກອບດ້ວຍຊຸດຊັບພະຍາກອນ*ປະເພດດຽວກັນ* ຕົວຢ່າງ ຜູ້ໃຊ້ມີຄໍເຣັກຊັ່ນຂອງຊ່ອງຕິດຕໍ່.
- ຊັບພະຍາກອນມີສະຖານະ ແລະຊັບພະຍາກອນຍ່ອຍເປັນສູນ ຫຼືຫຼາຍກວ່າ. ຊັບພະຍາກອນຍ່ອຍແຕ່ລະຢ່າງສາມາດເປັນຊັບພະຍາກອນງ່າຍໆ ຫຼື ຊັບພະຍາກອນຄໍເຣັກຊັ່ນ.

ຕົວຢ່າງເຊັ່ນ Gmail API ມີຄໍເຣັກຊັ່ນຂອງຜູ້ໃຊ້ ຜູ້ໃຊ້ແຕ່ລະຄົນຈະມີຄໍເຣັກຊັ່ນຂໍ້ຄວາມ, ຄໍເຣັກຊັ່ນເທຣດ, ຄໍເຣັກຊັ່ນ label, ໂປຣໄຟລ ແລະການຕັ້ງຄ່າຕ່າງໆ.

ເຖິິງວ່າຈະມີການວາງແນວຄວາມຄິດລະຫວ່າງລະບົບຈັດເກັບຂໍ້ມູນ ແລະ REST API ແຕ່ບໍລິການທີ່ມີ resource-oriented API ບໍ່ຈຳເປັນຕ້ອງເປັນຖານຂໍ້ມູນ ແລະມີຄວາມຫຍືດຫຍຸ່ນໃນການຫຼາຍໃນການຕີຄວາມໝາຍຊັບພະຍາກອນ ແລະວິທີການ. ຕົວຢ່າງ ການສ້າງກິດຈະກຳໃນປະຕິທິນ (ຊັບພະຍາກອນ) ອາດຈະສ້າງກິດຈະກຳເພີ່ມເຕີມສຳລັບຜູ້ເຂົ້າຮ່ວມ, ສົ່ງຄຳເຊີນທາງອີເມວຫາຜູ້ເຂົ້າຮ່ວມ, ຈອງຫ້ອງປະຊຸມ ແລະອັບເດດກຳນົດການປະຊຸມທາງວິດີໂອ.

## Methods

ລັກສະນະສຳຄັນຂອງ resource-oriented API ແມ່ນການເນັ້ນຊັບພະຍາກອນ (data model) ຫຼາຍກວ່າວິທີການທີ່ກະທຳກັບຊັບພະຍາກອນ (functionality). ໂດຍທົ່ວໄປ resource-oriented API ຈະສົ່ງອອກ (expose) ຊັບພະຍາກອນຈຳນວນຫຼາຍດ້ວຍຊຸດວິທີການພຽງເລັກນ້ອຍ. ວິທີການອາດເປັນໄດ້ທັງວິທີການມາດຕະຖານ ຫຼືວິທີການທີ່ກຳນົດເອງ ສຳລັບຄຳແນະນຳວິທີການມາດຕະຖານໄດ້ແກ່ `List`, `Get`, `Create`, `Update` ແລະ `Delete`.

ໃນກໍລະນີທີ່ການເຮັດວຽກຂອງ API ຈັບຄູ່ກັບໜຶ່ງໃນວິທີມາດຕະຖານ *ຄວນ*ໃຊ້ວິທີນັ້ນໃນການອອກແບບ API. ສຳລັບຟັງຊັ່ນການເຮັດວຽກທີ່ບໍ່ສາມາດຈັບຄູ່ກັບວິທີມາດຕະຖານຢ່າງໃດໜຶ່ງ *ອາດ*ໃຊ້ວິທີການແບບກຳນົດເອງ ([Custom method](#custom-methods)) ເຊິ່ງໃຫ້ອິດສະຫຼະໃນການອອກແບບເຊັ່ນດຽວກັບ RPC API ເຮັດໃຫ້ສາມາດໃຊ້ຮູບແບບທົ່ວໄປໃນການຂຽນໂປຣແກຣມໄດ້ ເຊັ່ນ database transaction ຫຼື ການວິເຄາະຂໍ້ມູນ.

# Resource Names

# Standard Methods

# Custom Methods

# Standard Fields

# Errors

# Naming Conventions

# Design Patterns

# Documentation

# Using proto3

# Versioning

# Compatibility

# Directory Structure

# File Structure

# Glossary

# Changelog
