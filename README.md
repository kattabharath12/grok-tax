# TaxGrok - AI-Powered Tax Filing Application

TaxGrok is a modern, full-stack tax filing application that uses AI to automatically extract data from tax documents and simplify the tax filing process.

## Features

- 🔐 **Secure Authentication** - NextAuth.js with email/password authentication
- 📄 **Document Upload & OCR** - Upload W-2, 1099, and other tax forms
- 🤖 **AI-Powered Extraction** - Azure Document Intelligence for automatic data extraction
- 💾 **PostgreSQL Storage** - All data and files stored securely in PostgreSQL database
- 📊 **Tax Calculations** - Automated federal and state tax calculations
- 📝 **Form 1040 Generation** - Generate completed Form 1040 PDFs
- 🎨 **Modern UI** - Built with Next.js 14, React, and Tailwind CSS
- 🚀 **Railway Ready** - Optimized for deployment on Railway platform

## Tech Stack

- **Framework**: Next.js 14 (App Router)
- **Language**: TypeScript
- **Database**: PostgreSQL with Prisma ORM
- **Authentication**: NextAuth.js
- **OCR/AI**: Azure Document Intelligence
- **UI**: Tailwind CSS + Radix UI components
- **File Storage**: PostgreSQL (bytea)
- **PDF Generation**: pdf-lib, jsPDF

## Prerequisites

- Node.js 18+ and npm/yarn
- PostgreSQL database
- Azure Document Intelligence resource (for OCR)

## Environment Variables

Create a `.env` file in the root directory:

```env
# Database
DATABASE_URL="postgresql://username:password@host:port/database"

# NextAuth Secret (generate using: openssl rand -base64 32)
NEXTAUTH_SECRET="your-secret-here"

# Azure Document Intelligence (for OCR processing)
AZURE_DOC_INTELLIGENCE_API_KEY="your-azure-api-key"
AZURE_DOC_INTELLIGENCE_ENDPOINT="https://your-resource.cognitiveservices.azure.com/"
```

## Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd taxgrok-railway-postgresql
   ```

2. **Install dependencies**
   ```bash
   npm install
   # or
   yarn install
   ```

3. **Set up environment variables**
   ```bash
   cp .env.example .env
   # Edit .env with your actual values
   ```

4. **Initialize the database**
   ```bash
   npx prisma generate
   npx prisma db push
   ```

5. **Run the development server**
   ```bash
   npm run dev
   # or
   yarn dev
   ```

6. **Open your browser**
   Navigate to [http://localhost:3000](http://localhost:3000)

## Database Schema

The application uses PostgreSQL with the following key models:

- **User** - User accounts with authentication
- **Document** - Uploaded tax documents with file data stored as bytea
- **ExtractedData** - AI-extracted fields from documents
- **TaxReturn** - Tax return data and calculations

### File Storage

All uploaded files are stored directly in the PostgreSQL database using the `bytea` (binary) type. This eliminates the need for external file storage services like AWS S3 and simplifies deployment.

## Deployment on Railway

Railway provides a simple, one-click deployment experience with built-in PostgreSQL.

### Prerequisites

1. **Railway Account** - Sign up at [railway.app](https://railway.app)
2. **Azure Document Intelligence** - Create a resource in Azure Portal

### Deployment Steps

1. **Create New Project**
   - Go to [railway.app/new](https://railway.app/new)
   - Click "Deploy from GitHub repo"
   - Select your TaxGrok repository

2. **Add PostgreSQL Database**
   - In your project, click "New"
   - Select "Database" → "PostgreSQL"
   - Railway will automatically set `DATABASE_URL`

3. **Configure Environment Variables**
   - Go to your service → Variables
   - Add the following:
     - `NEXTAUTH_SECRET` - Generate with `openssl rand -base64 32`
     - `AZURE_DOC_INTELLIGENCE_API_KEY` - From Azure Portal
     - `AZURE_DOC_INTELLIGENCE_ENDPOINT` - From Azure Portal

4. **Deploy**
   - Railway will automatically build and deploy
   - Database migrations run automatically on first deploy

5. **Access Your App**
   - Click on your service to get the public URL
   - Format: `https://your-app.up.railway.app`

### Post-Deployment

The application will:
- Automatically run Prisma migrations
- Generate Prisma Client
- Build the Next.js application
- Start the production server

## Project Structure

```
taxgrok-railway-postgresql/
├── app/                      # Next.js App Router
│   ├── api/                  # API routes
│   │   ├── auth/            # NextAuth endpoints
│   │   ├── upload/          # File upload (stores in PostgreSQL)
│   │   ├── documents/       # Document management
│   │   ├── process-document/ # OCR processing
│   │   └── tax-calculation/ # Tax calculations
│   ├── auth/                # Auth pages (login/signup)
│   ├── dashboard/           # User dashboard
│   ├── file-return/         # Tax filing wizard
│   └── layout.tsx           # Root layout
├── components/              # React components
│   ├── auth/               # Authentication components
│   ├── dashboard/          # Dashboard components
│   ├── tax-filing/         # Tax filing wizard steps
│   └── ui/                 # Reusable UI components
├── lib/                     # Utility libraries
│   ├── auth-options.ts     # NextAuth configuration
│   ├── azure-client.ts     # Azure Document Intelligence
│   ├── db.ts               # Prisma client
│   ├── tax-calculations.ts # Tax calculation logic
│   └── f1040-pdf-generator.ts # Form 1040 PDF generation
├── prisma/
│   └── schema.prisma       # Database schema
├── public/                  # Static assets
└── package.json            # Dependencies

```

## Key Features Explained

### Document Upload & Storage

- Files are uploaded via multipart/form-data
- Converted to Buffer and stored in PostgreSQL `bytea` column
- No external storage service required
- Maximum file size: 10MB per document
- Supported formats: PDF, PNG, JPG, JPEG, TIFF

### AI-Powered Data Extraction

- Uses Azure Document Intelligence (formerly Form Recognizer)
- Automatically detects document type (W-2, 1099, etc.)
- Extracts key fields with confidence scores
- Manual review and editing supported

### Tax Calculations

- Supports both federal and state tax calculations
- Implements 2024 tax brackets and rates
- Calculates standard deductions
- Generates estimated tax liability

## Development

### Database Migrations

```bash
# Create a new migration
npx prisma migrate dev --name migration_name

# Apply migrations
npx prisma migrate deploy

# Reset database (development only)
npx prisma migrate reset
```

### Prisma Studio

```bash
# Open Prisma Studio to view/edit data
npx prisma studio
```

### Build for Production

```bash
npm run build
npm start
```

## Security Considerations

- All routes are protected with authentication
- File uploads validated for type and size
- User data isolated by userId
- Environment variables for sensitive data
- HTTPS enforced in production

## Troubleshooting

### Database Connection Issues

- Verify `DATABASE_URL` is correct
- Check PostgreSQL is running
- Ensure database exists

### Azure OCR Errors

- Verify Azure credentials in `.env`
- Check Azure resource region matches endpoint
- Ensure sufficient Azure credits

### File Upload Issues

- Check file size (max 10MB)
- Verify file type (PDF, PNG, JPG, TIFF)
- Ensure database has sufficient storage

## Performance Considerations

### Database File Storage

Storing files in PostgreSQL is suitable for:
- Small to medium-sized files (< 10MB)
- Applications with moderate file upload volume
- Simplified deployment without external dependencies

For high-volume applications with large files, consider:
- Implementing file compression
- Adding file size monitoring
- Using connection pooling (Prisma already does this)

## Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a Pull Request

## License

MIT License - see LICENSE file for details

## Support

For issues and questions:
- Create an issue on GitHub
- Check existing issues for solutions
- Review Railway deployment logs

## Changelog

### Version 2.0 (Current)
- ✅ Removed AWS S3 dependency
- ✅ Migrated to PostgreSQL file storage
- ✅ Simplified deployment process
- ✅ Railway-optimized configuration

### Version 1.0
- Initial release with AWS S3 storage
- Azure Document Intelligence integration
- Tax calculation engine
- Form 1040 PDF generation
