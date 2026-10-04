# Prompt for Manus AI: Build a Standalone Android Attendance APK

Build a **standalone native Android APK** for the attendance application in this repository:

**Repository:** https://github.com/hhi-wasit/attendance-v2

The APK must preserve the functionality and Arabic RTL behavior of the current web app, while becoming a real Android application that can be downloaded and installed directly on an Android phone.

## Primary deliverable

Produce a **single signed, installable APK** that the user can download and install directly on an Android device.

The final user must not need:

- Android Studio
- Node.js
- Python
- A browser
- Excel or Word installed on the phone
- A server, backend, or companion application
- Any other application to operate the attendance system

The APK must generate and save PDF and Word files internally. Android may show its normal system permission prompts, including camera permission and the standard permission required to install an APK downloaded outside Google Play.

Do not deliver only a WebView wrapper or a website. Build a proper native Android app, preferably with **Kotlin and Jetpack Compose**.

## Language and design

- The complete user interface must be Arabic.
- Use `ar` locale and true **RTL** layout throughout the application.
- Use a readable Arabic font available on Android.
- Keep the current visual style: dark navy/teal interface, large department buttons, clear attendance colors, mobile portrait layout.
- All Arabic labels, dialogs, lists, tables, generated documents, and exported files must be RTL.
- The app must work well on common Android phones in portrait orientation.

## Departments and roster files

The app must support these six departments/classes:

1. الطوارئ — `emergency.xlsx`
2. القبالة — `midwifery.xlsx`
3. التمريض كروب A — `nursing A.xlsx`
4. التمريض كروب B — `nursing B.xlsx`
5. التمريض كروب C — `nursing C.xlsx`
6. التمريض كروب D — `nursing D.xlsx`

The current repository contains these files in the `attendance lists` folder. The native APK must support both of these workflows:

### Bundled default lists

- Include the six current repository Excel files in the APK as default roster files, or copy them into the app’s private storage on first launch.
- The app must work with the bundled lists without internet access.

### User upload/update workflow

Provide an Arabic interface to upload or replace the Excel list for each department from the phone’s file picker.

For every department, the user must be able to:

- Choose the department.
- Select an `.xlsx` file from the phone.
- Import it entirely inside the app.
- Validate it and show a useful Arabic error if the file is invalid or contains no student names.
- Save the imported list in the app’s private storage.
- Use the updated list immediately for attendance.

The app must not require Microsoft Excel or any other app to read the uploaded workbook.

## Excel roster parsing

Support normal `.xlsx` files and map common Arabic and English column headers.

At minimum recognize:

- Student number: `ت`, `الرقم`, `رقم`, `id`, `no`, `number`
- Student name: `اسم`, `اسم الطالب`, `الاسم`, `name`
- Department: `القسم`, `department`, `dept`
- Stage/year: `المرحلة`, `مرحلة`, `stage`, `level`, `year`
- Group/class: `المجموعة`, `مجموعة`, `الشعبة`, `شعبة`, `group`

The name column is required. Preserve the other available fields as student details.

## Department selection behavior

Initial screen:

- Show the six department buttons.
- Do not show the camera panel or scan controls before a department is selected.

After the user selects a department:

1. Load that department’s saved Excel roster.
2. Display the complete list of students immediately.
3. Show every student with a status:
   - `حاضر` in green
   - `غائب` in red
4. Open the camera scanner automatically after the roster loads.
5. Request camera permission only when needed.
6. If another department was open, stop its camera first and switch cleanly.

## QR attendance scanning

Use the phone’s rear camera to scan student QR codes.

- Use CameraX with ML Kit barcode scanning, ZXing, or another reliable native QR scanner.
- Support QR code content containing student number, name, department, group, stage, or labeled Arabic/English fields.
- Match a scanned student against the selected department’s imported roster.
- Match by student number when available, otherwise by normalized student name and available details.
- If the scanned student is not in the selected roster, show an Arabic warning and do not mark attendance.
- Prevent duplicate attendance records for the same student.
- Show the latest scan result clearly.
- Provide sound/vibration feedback for successful, duplicate, and invalid scans.
- Include a clear button to stop scanning.
- When scanning stops, finalize the attendance automatically.

## Attendance list behavior

The selected department’s full roster must remain visible during and after scanning.

Each row must include:

- Serial number
- Student name
- Imported details such as department, stage, and group when available
- Attendance status
- Scan time for present students

Status rules:

- Scanned student: `حاضر`, green
- Not scanned student: `غائب`, red
- The list must include absent students, not only scanned students.
- Sort by original Excel order by default.
- Provide an optional Arabic/English alphabetical sort control if practical.

## Session fields

For every attendance session, provide compact editable fields for:

- `اسم المحاضر`
- `المادة`

Save these values with the session and include them in every export.

Also preserve:

- Department/class name
- Session title if used
- Session date
- Attendance records
- Finalized state

## Sessions and persistence

The app must work offline after installation.

Persist data locally using Room or another reliable native local database. Store:

- Imported department rosters
- Current and previous attendance sessions
- Lecturer name
- Subject
- Department
- Student attendance status and scan time
- Export metadata

Provide a sessions screen in Arabic where the user can:

- Open a previous session
- Create a new session
- Delete a session with a confirmation dialog

Do not lose attendance data when the app is closed or the phone is restarted.

## PDF export

Generate the PDF natively inside the APK; do not require another app or an online service.

The PDF must match this reference arrangement:

- RTL Arabic document.
- Department/session title at the upper-right.
- At the upper-right, compact lines in this exact logical arrangement:
  - `المادة: ____________________`
  - `اسم المحاضر: ____________________`
- At the upper-left:
  - Date
  - Number of present students
- The full attendance table directly below the header.
- RTL table order, visually matching the reference:
  - `ت` / serial number on the right
  - `الاسم`
  - `التفاصيل`
  - `التاريخ`
  - `الوقت`
  - `الحالة` on the left
- `حاضر` must be green.
- `غائب` must be red.
- Preserve Arabic student names correctly.
- Put export date/time in the lower-left.
- Put `التوقيع: ______________________________` at the lower-right footer.
- Keep the header and footer compact so they do not waste space or unnecessarily reduce the student table area.
- For multiple pages, repeat the table header and place the signature footer on the final page.

## Word export

Generate a real `.docx` file natively inside the APK using OpenXML generation or a bundled offline library. Do not require Microsoft Word to generate the file.

The Word document must:

- Be a valid openable `.docx` file.
- Use Arabic RTL document, paragraph, table, and section settings.
- Match the same visual arrangement as the PDF:
  - Title at the upper-right.
  - `المادة: blank` and `اسم المحاضر: blank` at the upper-right.
  - Date and present count at the upper-left.
  - Full RTL attendance table.
  - Export date/time at the lower-left.
  - `التوقيع: ______________________________` at the lower-right.
- Color `حاضر` green and `غائب` red in the Word table.
- Include every roster student, including absent students.
- Preserve Arabic text and names correctly.

## Save and share behavior

Provide a clear Arabic toggle or segmented control:

- `مشاركة`
- `حفظ`

When `حفظ` is selected:

- Save the generated PDF or DOCX to a user-accessible Downloads/Documents location using Android’s storage APIs.
- Show an Arabic success message with the filename.

When `مشاركة` is selected:

- Open Android’s native share sheet with the generated PDF or DOCX file.
- Share the actual file, not only text or a URL.
- DOCX sharing must not silently fall back to download because of an unreliable MIME pre-check. Attempt native sharing directly, and use a compatible generic MIME fallback if required.
- If the user cancels sharing, do not show an error.
- Do not require a third-party app to generate the file.

## Permissions and privacy

Request only the permissions needed:

- Camera permission when scanning begins.
- File picker/storage access only through modern Android document APIs when importing or saving.

Keep all roster and attendance data local to the device. Do not upload student data to a server.

## Error handling

Show clear Arabic messages for:

- Missing or invalid Excel file
- Excel file without a student-name column
- Empty roster
- Camera permission denied
- Camera unavailable
- QR code not recognized
- Student not found in selected department
- Duplicate scan
- Export generation failure
- Share failure
- Save failure

## Acceptance tests

Before delivering the APK, verify all of the following on a real or emulated Android device:

1. Install the APK directly without Android Studio or a companion application.
2. Launch the app offline.
3. Confirm the six department buttons are visible in Arabic.
4. Confirm the camera panel is hidden initially.
5. Select each department and confirm its roster loads.
6. Upload a replacement `.xlsx` list for a department and confirm it is used.
7. Confirm the complete roster appears, including absent students.
8. Scan a valid student QR code and confirm that student becomes green `حاضر`.
9. Confirm unscanned students remain red `غائب`.
10. Scan a duplicate code and confirm no duplicate row is created.
11. Scan a student not in the selected roster and confirm an Arabic warning.
12. Stop scanning and confirm attendance is finalized.
13. Enter lecturer name and subject.
14. Export PDF and confirm the exact RTL reference-style arrangement.
15. Export Word and confirm the exact RTL reference-style arrangement.
16. Confirm `حاضر` is green and `غائب` is red in both files.
17. Confirm `التوقيع` appears in the lower-right footer.
18. Test both `حفظ` and `مشاركة` for PDF and DOCX.
19. Close and reopen the app and confirm sessions and imported lists remain available.
20. Test with Arabic names and long names.

## Final output required from Manus AI

Deliver all of the following:

1. A production-ready signed APK ready to download and install.
2. The Android source project.
3. A short Arabic installation and usage guide.
4. A list of requested Android permissions.
5. A test report confirming the acceptance tests above.
6. The exact APK filename, version, architecture, and SHA-256 checksum.

Do not stop at design mockups, pseudocode, or a WebView prototype. Continue until the downloadable APK is built, tested, and provided.
